# BloodHoundStudy

## Path 1
Knowing we want to get to an admin group, I added the domain admins@testlab.local to bloodhound to do a reverse search. 

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/b7d2799e-b2ce-45bd-9425-5e5183f38184)

I click on "Domain Admins" to see what I can work backwards from, to. (Right clikck and select, Shortest paths to here). 
![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/f6fef898-712c-494f-b7ac-4d6d707171e7)

From here we are able to identify a few paths to get to "Domain Admin" the main goal of most AD pentests.

## Path 2:
Using the knowledge gained in Path 1 coupled with the users we know we have access to. I selected DBLUMBERG00200@testlab.local as the first user. 
I then selected the Domain admins group as my end path and set the user DBL~ as the start path to get a clear picture of how to go from user to Domain admin. 

![image](https://github.com/AssassinUKG/BloodHoundStudy/assets/5285547/4b5b1074-bbfe-4fc6-a41b-af4882e1ba73)

### Path 2 Route
- `DBLUMBERG00200` has `AllExtendedRights` privilege to the `MBOLSTER00157@TESTLAB.LOCALE` from the `T1 PAW Maintenance@TESTLAB.LOCALE` group.   
- `MBOLSTER00157@TESTLAB.LOCALE` has `AddMember` permission to the `T2 Admin Accounts` groups. By adding itself to the group, `T1 Management@TESTLAB.LOCALE` will gain the same privileges that `T2 Admin Accounts Group@TESTLAB.LOCALE` already has.    
- We then have two options, we have T2 Admin rights over `PAW-00041` computer or `T2 Admin Accounts Group@TESTLAB.LOCALE` have the `AllExtendedRights` privilege to the user `DABASCAL00279@TESTLAB.LOCALE`. The user `DABASCAL00279@TESTLAB.LOCALE` has membership in the `Distributed COM` Users local group on the computer `PAW-00041@TESTLAB.LOCALE`.    
- The user `FKIRCHMANN00135@TESTLAB.LOCALE` has a session on the computer `PAW-00041@TESTLAB.LOCALE`. So we can use this session token inpernation and password theft.   
- The user `FKIRCHMANN00135@TESTLAB.LOCALE` is a member of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE`.    
- The members of the group `ACCOUNT OPERATORS@TESTLAB.LOCALE` have `GenericAll` privileges to the group `SERVER OPERATORS@TESTLAB.LOCALE`  
- The members of the group `SERVER OPERATORS@TESTLAB.LOCALE` have permissions to modify the `DACL (Discretionary Access Control List)` on the group `DOMAIN ADMINS@TESTLAB.LOCALE`   


