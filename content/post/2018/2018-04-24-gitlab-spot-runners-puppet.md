---
title: 'GitLab spot runners & Puppet'
author: penguin
type: post
date: 2018-04-24T18:10:31+00:00
url: /2018/04/24/gitlab-spot-runners-puppet/
categories:
  - CICD
  - Puppet
  - Snippets
tags:
  - aws
  - gitlab

---
We are on AWS with GitLab. For ease of use, and because our build hosts degenerate for some reason (network issues), we decided to use spot instances with GitLab.

The journey was all but easy. Here's why.

### GitLab Runner configuration complaints

#### First: The process

To configure GitLab runner, you have to ...

  * install GitLab,
  * write down the runner registration token,
  * start a runner,
  * manually a registration command using above token.

That registration command will then _modify the config file of the runner_. That is important because you can't just write a static, read-only config file and start the runner. This is not possible for two reasons:

  * when you execute the registration command, the runner wants to modify the config file to add _yet another_ token (its "personal" token, not the general registration secret), so it must not be read-only
  * the runner has to be registered, so just starting it will do ... nothing.

That is in my eyes a huge design flaw, which undoubtedly has its reasons, but it still - sorry - sucks IMHO.

#### Second: The configuration

You can configure pretty much everything in the config file. But once the runner registers, the registration process for some reason _appends_ a completely new config to any existing config file, so that ... the state is weird. It works, but it looks fucked, and feels fucked.

You can also set all configuration file entries using the `gitlab-runner register`  command. Well, not all: The global parameters (like, for example, `log_level`  or `concurrent` ) cannot be set. _Those_ have to be in a pre-existing config file, so you need _both_ - the file _and_ the registration command, which will look super ugly in a very short time.

Especially if you still use Puppet to manage the runners, cause then you just can't just restart the runner once the config file changes. Because it _will always_ change, because of above reasons.

#### Third: The AWS permission documentation

Another thing is that the list of AWS permissions the runner needs in order to create spot instances is nowhere to be found. Hint: `EC2FullAccess`  and `S3FullAccess` is _not_ enough. We are using admin permissions right now, until we figured it out. Not nice.

### Our solution

For this we're still using Puppet (our K8S migration is still ongoing), and our solution so far looks like this:

  * Create a config file with puppet _next to_ the designated config file location, 
      * containing _only_ global parameters.
      * The file has a puppet hook which triggers an exec that deletes the "final" config file if the puppet-created one has changed.
  * Start the GitLab runner.
  * Perform a "docker exec" which registers the runner in GitLab. 
      * The "unless" contains a check that skips execution if the final config file is present.
      * The `register`  command sets all configuration values except the global ones. Like said above, the command appends all non-global config settings to any existing config file.

### Some code

```yaml
#
# CONFIG VALUES
#

# the configuration for the build runner running on the same host, which
# manages the autoscaling-based spot-instance-allocation
global::concurrent:                       '5'

registration_command::docker_image:       'ubuntu:artful'
registration_command::token:              'our-token'
registration_command::url:                'https://our.gitlab.server'

runners::concurrency:                     '1'
runners::limit:                           '10'
runners::name:                            'spot-runner'

cache::bucket_location:                   'eu-central-1'
cache::bucket_name:                       'our-cache-bucket'
cache::shared:                            'true'
cache::type:                              's3'

machine::idle_nodes:                      '0'
machine::idle_time:                       '1800'
machine::max_builds:                      '10'
machine::machine_name:                    'standard-%s'

machine::option::access_key:              "%{hiera('ops::gitlab::spotrunner::id')}"
machine::option::ami:                     'ami-44d48eaf'
machine::option::block_minutes:           '60'
machine::option::iam_profile:             'gitlab-runner'
machine::option::instance_type:           'm4.xlarge'
machine::option::private_address_only:    'true'
machine::option::private_address:         'true'
machine::option::region:                  'eu-central-1'
machine::option::secret_key:              "%{hiera('ops::gitlab::spotrunner::secret')}"
machine::option::spot_instances:          'true'
machine::option::spot_price:              '0.2'
machine::option::subnet_id:               'subnet-subbb'
machine::option::tags:                    'stage,prod'
machine::option::vpc_id:                  'vpc-veepeecee'

machine::option::other:                   &gt;
  --machine-machine-options amazonec2-security-group=cognodev3_sg_docker_machine
  --machine-machine-options amazonec2-security-group=cognodev3-SG0-shubdu
  --machine-machine-options amazonec2-security-group=cognodev3-SG1-shalala
  --machine-machine-options amazonec2-security-group=cognodev3-SG2-shubndibndu
  --machine-machine-options engine-insecure-registry=some.internal.repo
  --machine-machine-options engine-insecure-registry=other.internal.repo:5000



#
# START
#

## GITLAB SPOT INSTANCE RUNNER

create::docker_containers:
  # gitlab spot runner
  gitlab-spot-runner:
    image:              gitlab/gitlab-runner:latest
    pull_on_start:      true
    volumes:
      - /var/docker-apps/gitlab-spot-runner:/etc/gitlab-runner
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/run/.docker:/root/.docker

create::files:
  '/var/docker-apps/gitlab-spot-runner/config.toml.puppet':
    ensure: present
    notify: 'Service[docker-gitlab-spot-runner]'
    content: |
      concurrent = %{hiera('global::concurrent')}
      check_interval = 0


create::execs:
  'delete config file':
    command:     rm /etc/gitlab-runner/config.toml
    path:        /bin:/usr/bin:/sbin:/usr/sbin
    refreshonly: true
    subscribe:   File[/etc/gitlab-runner/config.toml.puppet]
    before:      Docker::Exec[register-spot-runner]

# why are we doing this?
# because gitlab WANTS to re-write the config file. and we CANNOT set "global"
# parameters (e.g. "log_level", "concurrent") in using the registration
# command line.
# so, we ...
#    * create a config "config.toml.puppet" file with ONLY global params
#    * rm the config.toml file in case of changes to the .puppet file
#    * re-execute registration using ALL other config settings when the
#      config.toml file is no longer present
# we do this because:
#    * we can't watch the toml file for changes, cause the runner WILL change
#      it
#    * but we WANT to set global parameters, and luckily the runner seems
#      to just append all non-global settings to an existing config file
#      on registration.
# THIS FUCKING SUCKS. I know. but I really have not found another way (abk)

create::docker_execs:
  'register-spot-runner':
    detach:     false
    container:  gitlab-spot-runner
    tty:        true
    command: &gt;-
      /bin/bash -c 'cp /etc/gitlab-runner/config.toml.puppet /etc/gitlab-runner/config.toml &&
      gitlab-runner register --non-interactive
      --registration-token %{hiera('registration_command::token')}
      --url %{hiera('registration_command::url')}
      --executor "docker+machine"
      --docker-image %{hiera('registration_command::docker_image')}
      --locked=false
      --run-untagged
      --docker-volumes /var/run/docker.sock:/var/run/docker.sock
      --name %{hiera('runners::name')}
      --limit %{hiera('runners::limit')}
      --request-concurrency %{hiera('runners::concurrency')}
      --cache-cache-shared=%{hiera('cache::shared')}
      --cache-s3-bucket-location %{hiera('cache::bucket_location')}
      --cache-s3-bucket-name %{hiera('cache::bucket_name')}
      --cache-type %{hiera('cache::type')}
      --machine-machine-driver amazonec2
      --machine-idle-nodes %{hiera('machine::idle_nodes')}
      --machine-idle-time  %{hiera('machine::idle_time')}
      --machine-machine-name %{hiera('machine::machine_name')}
      --machine-max-builds %{hiera('machine::max_builds')}
      --machine-machine-options amazonec2-private-address-only=%{hiera('machine::option::private_address_only')}
      --machine-machine-options amazonec2-use-private-address=%{hiera('machine::option::private_address')}
      --machine-machine-options amazonec2-region=%{hiera('machine::option::region')}
      --machine-machine-options amazonec2-access-key=%{hiera('machine::option::access_key')}
      --machine-machine-options amazonec2-ami=%{hiera('machine::option::ami')}
      --machine-machine-options amazonec2-block-duration-minutes=%{hiera('machine::option::block_minutes')}
      --machine-machine-options amazonec2-iam-instance-profile=%{hiera('machine::option::iam_profile')}
      --machine-machine-options amazonec2-instance-type=%{hiera('machine::option::instance_type')}
      --machine-machine-options amazonec2-request-spot-instance=%{hiera('machine::option::spot_instances')}
      --machine-machine-options amazonec2-secret-key=%{hiera('machine::option::secret_key')}
      --machine-machine-options amazonec2-spot-price=%{hiera('machine::option::spot_price')}
      --machine-machine-options amazonec2-subnet-id=%{hiera('machine::option::subnet_id')}
      --machine-machine-options amazonec2-tags=%{hiera('machine::option::tags')}
      --machine-machine-options amazonec2-vpc-id=%{hiera('machine::option::vpc_id')}
      %{hiera('machine::option::other')}'
    unless:     test -f /etc/gitlab-runner/config.toml
    require:    Service[docker-gitlab-spot-runner]
```

Does this look ugly? _You bet._

Should this be a puppet module? _Most probably._

Did I foresee this? _Nope._

Am I completely fed up? _Yes._

Is this stuff I want to do? _No._

Does it work?

... **Yes** (at least ... 🙂 )

### Remarks

If you wander what all those `create::THING`  entries are - it's this:

```default
$files = hiera_hash('create::files', {})
if ! empty($files) {
  create_resources('file', $files)
}
```

We have an awful lot of those, cause then we can do a _lot_ of stuff in the config YAMLs and don't need to go in puppet DSL code.