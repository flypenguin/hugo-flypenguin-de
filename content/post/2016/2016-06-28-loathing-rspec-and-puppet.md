---
title: Loathing RSpec and Puppet
author: penguin
type: post
date: 2016-06-28T08:07:24+00:00
url: /2016/06/28/loathing-rspec-and-puppet/
categories:
  - Puppet
tags:
  - rant
  - rspec
  - rspec-puppet
  - setup
  - testing

---
There _are_ words for how much I hate RSpec (especially RSpec-Puppet), but they would be too harsh to write down.

So now that I don't have to google the same shit over and over again, here's what you have to do to get basic puppet module testing up and running (replace $MODULE with your module name, of course):

### $MODULE/Rakefile

<pre>require 'rubygems'
require 'puppetlabs_spec_helper/rake_tasks'</pre>

### $MODULE/.fixtures.yml

<pre>fixtures:
  repositories:
    concat: git://github.com/puppetlabs/puppetlabs-concat.git
    # alternate method, for specifying refs
    stdlib: 
      repo: git://github.com/puppetlabs/puppetlabs-stdlib.git
      ref:  1.0.0
  symlinks:
    # do _not_ forget this
    $MODULE: "#{source_dir}"</pre>

### $MODULE/spec/spec_helper.rb

<pre>require 'rubygems'
require 'puppetlabs_spec_helper/module_spec_helper'</pre>

### $MODULE/spec/classes/$MODULE_spec.rb

<pre># see also http://rspec-puppet.com/
require 'spec_helper'

describe '$MODULE' do
  context 'default' do
    it {
      should contain_file('/etc/haproxy/haproxy.conf')
    }

    # or ...
    it do
      is_expected.to contain_file('/this/syntax/is/even/more/retarded')
    end
  end
end</pre>

### Final note

It's "rake spec", _not_ "rake test". Of course.