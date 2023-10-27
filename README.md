# BloodHoundStudy

Richard Jones
27/10/2023

## Initial Study
Knowing we wanted to get to an admin group, I added the domain admins@testlab.local to bloodhound to do a reverse search. 

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/b7d2799e-b2ce-45bd-9425-5e5183f38184)

I click on "Domain Admins" to see what I can work backward from, to. (Right clikck and select, Shortest paths to here). 
![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/f6fef898-712c-494f-b7ac-4d6d707171e7)

From here we are able to identify a few paths to get to "Domain Admin" the main goal of most AD pentests.

## Path 1:
Using the knowledge gained in Path 1 coupled with the users we know we have access to. I selected DBLUMBERG00200@testlab.local as the first user. 
I then selected the Domain admins group as my end path and set the user DBL~ as the start path to get a clear picture of how to go from user to Domain admin. 

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/4b5b1074-bbfe-4fc6-a41b-af4882e1ba73)

### Path 1 Route (User:DBLUMBERG00200 > Domain Admin)
- `DBLUMBERG00200` has `AllExtendedRights` privilege to the `MBOLSTER00157@TESTLAB.LOCALE` from the `T1 PAW Maintenance@TESTLAB.LOCALE` group.   
- `MBOLSTER00157@TESTLAB.LOCALE` has `AddMember` permission to the `T2 Admin Accounts` groups. By adding itself to the group, `T1 Management@TESTLAB.LOCALE` will gain the same privileges that `T2 Admin Accounts Group@TESTLAB.LOCALE` already has.    
- We then have two options
   1. we have `T2 Admin rights` over `PAW-00041@TESTLAB.LOCALE` computer.
   2.  or `T2 Admin Accounts Group@TESTLAB.LOCALE` have the `AllExtendedRights` privilege to the user `DABASCAL00279@TESTLAB.LOCALE`. The user `DABASCAL00279@TESTLAB.LOCALE` has membership in the `Distributed COM` Users local group on the computer `PAW-00041@TESTLAB.LOCALE`.    
- The user `FKIRCHMANN00135@TESTLAB.LOCALE` has a session on the computer `PAW-00041@TESTLAB.LOCALE`. So we can use this session token inspiration and password theft.   
- The user `FKIRCHMANN00135@TESTLAB.LOCALE` is a member of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE`.    
- The members of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE` have `GenericAll` privileges to the group `SERVER OPERATORS@TESTLAB.LOCALE`  
- The members of the group `SERVER OPERATORS@TESTLAB.LOCALE` have permissions to modify the `DACL (Discretionary Access Control List)` on the group `DOMAIN ADMINS@TESTLAB.LOCALE`   
- Then we can become the Domain Admin. (AD PWned)

## Path 2
Knowing we have admin privileges on one machine (`s-00042@testlabs.local`), lets start there. 

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/97438d18-5697-4c5d-a4c3-d773ed353ac9)

### Path 2 Route (PC: `S-00042@TESTLABS.LOCAL` > DOMAIN ADMIN)
- The user `LMENTION00250@TESTLAB.LOCALE` has a session on the computer `S-00042@TESTLAB.LOCALE`
- The user `LMENTION00250@TESTLAB.LOCALE` has the capability to create a `Remote Desktop Connection` with the computer `WS-00058@TESTLAB.LOCALE`
- The user `KCOSSANO00133@TESTLAB.LOCALE` has a session on the computer `WS-00058@TESTLAB.LOCALE` (mimikatz.eze / lsass.exe)
- The user `KCOSSANO00133@TESTLAB.LOCALE` is a member of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE`
- The members of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE` have `GenericAll` privileges to the group `SERVER OPERATORS@TESTLAB.LOCALE`
- The members of the group `SERVER OPERATORS@TESTLAB.LOCALE` have permissions to modify the `DACL (Discretionary Access Control List)` on the group `DOMAIN ADMINS@TESTLAB.LOCALE`
- Then we get the Domain Admin.

## Path 3
Starting from another of the known users `HPETO00139@TESTLAB.LOCALE` we can see a similar attack path to path 1. 

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/9d28ad9b-35ae-46ba-8645-038fdd1f4438)

### Path 3 Route (User: HPETO00139@TESTLAB.LOCALE > DOMAIN ADMIN)
- The user `HPETO00139@TESTLAB.LOCALE` is a member of the group `T1 Management@TESTLAB.LOCALE`
- The members of the group `T1 Management@TESTLAB.LOCALE` have the ability to add arbitrary principals, including themselves, to the group `T2 Admin Accounts Group@TESTLAB.LOCALE`. Because of security group delegation, the members of a security group have the same privileges as that group
- From the T2 Admin Accounts group we have two options.
   1. The members of the group `T2 Admin Accounts Group@TESTLAB.LOCAL` have admin rights to the computer `T2 Admin Devices@TESTLAB.LOCALE`, which can access `PAW-00041` computer
   2. The members of the group `T2 Admin Accounts Group@TESTLAB.LOCALE` have the `AllExtendedRights` privilege to the user `DABASCAL00279@TESTLAB.LOCALE`, The user `DABASCAL00279@TESTLAB.LOCALE` has membership in the `Distributed COM` Users local group on the computer `PAW-00041@TESTLAB.LOCALE`
- The user `FKIRCHMANN00135@TESTLAB.LOCALE` has a session on the computer `PAW-00041@TESTLAB.LOCALE`
- The user `FKIRCHMANN00135@TESTLAB.LOCALE` is a member of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE`
- The members of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE` have `GenericAll` privileges to the group `SERVER OPERATORS@TESTLAB.LOCALE`
- The members of the group `SERVER OPERATORS@TESTLAB.LOCALE` have permissions to modify the `DACL (Discretionary Access Control List)` on the group `DOMAIN ADMINS@TESTLAB.LOCALE`
- At this point we would have DOMAIN ADMIN access.

## There are many more paths in this Active Directory environment, I've only picked three for now

## Custom Queries
Source: https://github.com/CompassSecurity/BloodHoundQueries
Install
```
curl -o ~/.config/bloodhound/customqueries.json "https://raw.githubusercontent.com/CompassSecurity/BloodHoundQueries/master/BloodHound_Custom_Queries/customqueries.json"
```

Using custom queries we can gain a better understanding of the network, computers and users.   

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/4be0f19d-5c57-4d08-9fb7-5d02dce51886)

Using the shortest paths to the domain option we can see a clear image with paths to try.  
![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/b487fb1b-a298-4a05-9f1b-450fb9caf4c6)

One of the shortest paths being from a user we already have access to `JIENNACO00149@TESTLAB.LOCALE`

### JIENNACO00149@TESTLAB.LOCALE Attack Path Route
- `JIENNACO00149@TESTLAB.LOCALE` can RDP to PC `WS-00058@TESTLAB.LOCALE` which has a session for user `KCOSSANO00133@TESTLAB.LOCALE`
- `KCOSSANO00133@TESTLAB.LOCALE` is a member of `ACCOUNT OPERATORS@TESTLAB.LOCAL` which has `GenricAll` rights over the `SERVER OPERATORS@TESTLAB.LOCAL` group.
-  The `SERVER OPERATORS@TESTLAB.LOCAL` group has `WriteDacl` over the `DOMAIN ADMIN` group. Ending up as `DOMAIN ADMIN`

# Methods

I would use tools like mimikatz.exe or obfuscated versions to access various shells (PowerShell, cmd). I would try to remain stealthy by using custom-built tools that are slightly different from the publically available tools. 
If acting in a black hat way. I would assume I would be trying to steal or encrypt large amounts of data to gain more asset hold over the target. 
A black hat may consider the use of malware and zero day exploits rather than relying on known red teaming or penetration testing tools and methods. (more risky out-of-the-box thinking)

If none of the paths were viable for the hacker, they may consider to fall back on older tactics, like spear phishing to try and gain another foothold)
