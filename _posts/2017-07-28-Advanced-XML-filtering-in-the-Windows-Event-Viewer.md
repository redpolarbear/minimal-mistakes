---
title: Advanced XML filtering in the Windows Event Viewer
date: 2017-07-28
categories: [System Administrator]
tags: [Windows AD, Event Viewer]
---

Refer to: [Advanced XML filtering in the Windows Event Viewer](https://blogs.technet.microsoft.com/askds/2011/09/26/advanced-xml-filtering-in-the-windows-event-viewer/)

One day I got a request that need to monitor one specified event in the Windows Event Viewer. However the filter function the Event Viewer had didn't meet my needs. I'd like to filter the event by the username. After the searching around Google, I found that the manual XML filter query is very powerful. Below was the note when I was doing my job.

Server OS: Windows 2008 and later

- Open the **Event Viewer**, right click the **Custom Views** and choose **Create Custom View**;
- Click the **XML** tab, and check **Edit query manually**;
- Click **OK** to the warning popup.

Code Example:

```xml
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[
        EventData[Data[@Name='LogonType']='2']
        and
        EventData[Data[@Name='TargetUserName']='john.doe']
        and
        System[(EventID='4624')]
      ] 
    </Select>
  </Query>
</QueryList>
```

It's easy to read and understand. If need more advanced condition statement, please refer to the further reading as below:

- [Create a Custom View](http://technet.microsoft.com/en-us/library/cc709635.aspx)
- [Event Queries and Event XML](http://msdn.microsoft.com/en-us/library/bb399427(v=VS.90).aspx)
- [Consuming Events (Windows)](http://msdn.microsoft.com/en-us/library/dd996910(VS.85).aspx)

Additional information:
- Event ID for the Logon/Logoff - [link](https://technet.microsoft.com/en-us/library/dd772708(v=ws.10).aspx)
    > - 4624 An account was successfully logged on.
    > - 4625 An account failed to log on.
    > - 4648 A logon was attempted using explicit credentials.
    > - 4675 SIDs were filtered.
    > - 4634 An account was logged off.
    > - 4647 User initiated logoff.
    > - 4649 A replay attack was detected.
    > - 4778 A session was reconnected to a Window Station.
    > - 4779 A session was disconnected from a Window Station.
    > - 4800 The workstation was locked.
    > - 4801 The workstation was unlocked.
    > - 4802 The screen saver was invoked.
    > - 4803 The screen saver was dismissed.
    > - 5378 The requested credentials delegation was disallowed by     > - policy.
    > - 5632 A request was made to authenticate to a wireless   > - network.
    > - 5633 A request was made to authenticate to a wired network.