---
title: 'Powershell, O365 & Teams PSTN calling'
author: penguin
type: post
date: 2018-12-05T09:56:29+00:00
url: /2018/12/05/powershell-o365-teams-pstn-calling/
categories:
  - Uncategorized
tags:
  - o365
  - powershell

---
Unfortunately you **need** a Windows system to administer Office 365 with PowerShell. It's only API calls, but it's not (yet, hopefully) migrated to .NET Core. So Mac & Linux users are out of luck, although .NET Core should be more than capable to do this.

Anyhow.

If you want to administer **Teams** with PowerShell, you ... are in trouble. It's barely documented, and it sucks. Those are the steps to be done:

```
> $sess = New-CsOnlineSession ...
> Import-Module SkypeOnlineConnector ...
> Import-PSSession $sess
```

... and this should be it. Now all the PowerShell commands for Teams (in my case: Grant-CsTeamsUpgradePolicy) should be available.