---
title: How to Audit Successful Logon/Logoff and Failed Logons in Active Directory
date: 2017-07-27
categories: [System Administrator]
tags: [linux, Windows AD, GPO]
---

Refer to: [How to Audit Successful Logon/Logoff and Failed Logons in Active Directory](https://www.lepide.com/blog/audit-successful-logon-logoff-and-failed-logons-in-activedirectory/)

- **Audit Logon Events** policy defines the auditing of every user attempt to log on to or log off from a computer. The account logon events on the domain controllers are generated for domain account activities, whereas these events on the local computers are generated for the local user account activities.

- **Audit Account Logon Events** policy defines the auditing of every event generated on a computer, which is used to validate the user attempts to log on to or log off from another computer. Such account logon events are generated and stored on the domain controller, when a domain user account is authenticated on that domain controller. For local user accounts, these events are generated and stored on the local computer when a local user is authenticated on that computer.

## How-to
- **Run `gpmc.msc`** command to open Group Policy Management Console
- Create a new policy or Edit the **"Default Domain Controllers Policy"** and link to the OU you want to audit.
- In the Group Policy Management Edit (GPME) window:
    - Under **Computer Configuration** and expand it;
    - **Policies** -> **Windows Settings** -> **Security Settings** -> **Local Policies** -> **Audit Policy**;
    - In the right hand panel of GPME, either Double click on **“Audit logon events”** or Right Click -> Properties on **“Audit logon events”**;
    - A new window of **“Audit logon events”** properties will open. Check `Success` and `Failure` boxes and click **“Ok”**.
    - In the right hand panel of GPME, either Double click on **“Audit account logon events”** or Right Click -> Properties on **“Audit account logon events”**
    - A new window of **“Audit account logon events”** properties will open. Check `Success` and `Failure` boxes and Click on **“OK”**.
    - run `gpupdate /force` to update the GPO on the client.
- Done
- Then you may find out the audit event from the **Event Viewer** - **Security**.
