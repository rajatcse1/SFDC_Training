
# Trigger Scenarios: 

### Questions: 
---

##### Trigger Scenario 1:
> Write a trigger to update a field(let it be city) in all related opportunities, when same field(city) is update in account.
##### Trigger Scenario 2 : 
> Apex trigger to update account rating when the opportunity stage equals closed won
##### Trigger Scenario 3 : 
> Prevent an account from deleting with trigger, if it has 2 or more contacts.
##### Trigger Scenario 4 :
> When ever new account is created with annual revenue more than five lakhs then add wilson as account team member.
##### Trigger Scenario 5 : 
> When ever the Account is created with Industry as Banking then create a contact for account, Contact Lastname as Account name and contact phone as account phone.
##### Trigger Scenario 6 : 
> When ever a case is created with origin as email then set status as new and Priority as Normal
##### Trigger Scenario 7 : 
> When ever lead is created with leadSource as Web then give rating as cold otherwise hot
##### Trigger Scenario 8 : 
> whenever a account phone is modified/updated then contact records with phone fields(otherphone with oldvalue and homephone with newvalue) associated with this account record gets updated as same as account phone field.
##### Trigger Scenario 9 : 
> When ever opportunity stagename is modifed to closed won then set closedate as today and type as new Customer.
##### Trigger Scenario 10 : 
> when a new opportunity is created with amount more than 50 lakhs add the wilson as opportunityteamMember.
##### Trigger Scenario 11 : 
> whenever the stagename is modified to closedwon then set the closedate as today
##### Trigger Scenario 13 : 
> when a new contact is created for a existing account then set contact otherphone as account phone.
##### Trigger Scenario 14 : 
> Create “Top X Designation” custom object which is the related list to Opportunity (Look up Relationship). In the Top X Designation object, create the fields Type (Picklist), Document Attached (Checkbox) Create one field Handoff Attached with pick list type with values are Yes, No on Opportunity Object. Logic :- If Type (Top X Designation) = “Contract Flow Down/Handoff”, and “Document Attached” = True then “Handoff Attached” = True, otherwise false.
##### Trigger Scenario 15 : 
> The following Trigger will fires when we try to create the account with same name i.e. Preventing the users to create Duplicate Accounts
##### Trigger Scenario 16 : 
> Trigger to count the total number of contact records associated with that Account
##### Trigger Scenario 17:- 
> Create the object called “Customer” and create the Master-Detail Relationship on Customer object so that Customer will be the related list to Account record. Create the field called “Account Manager” on Customer object which is lookup to the user object. Now Logic is when we create Customer record for account record, then the user in Account Manager field will be automatically added to Account Team of that associated account.
##### Trigger Scenario 18 : 
> 1. Set the OWD on the opportunity as private 
> 2. Remove modify all permission on wilson’s profile 
> 3. whenever the opportunity stagename is modified to closed won and amount is more than 10 lacs share the record with wilson
##### Trigger Scenario 19 : 
> When a new Account record is inserted verify the industry field value, if industry field value is Education then assign the owner as karthik.
##### Trigger Scenario 20 :
> When ever we are trying to delete the account record which has a contacts for it .,Then deletion has to fail
##### Trigger Scenario 21 : 
> When ever account record is deleted successfully send the email confirmation alert to the Account email address.
##### Trigger Scenario 22 : 
> 1. Create two objects credit card and Application . 
> 2. Create lookup field on Application choosing Credit card as parent
> 3. When we delete any credit card record all corresponding application records also be deleted.
#### Trigger Scenario 30 :
> 
### Answer:
---
##### Trigger Scenario 1:

> Write a trigger to update a field(let it be city) in all related opportunities, when same field(city) is update in account.
```java
trigger City_Opportunity on Account(after update) {
 list < opportunity > oppor = new list < opportunity > ();
 for (opportunity opp: [select stagename, closedate, city__c from opportunity]) {
  for (account a: trigger.old) {
   if (a.city__c == ‘Hyderabad’) {
    opp.city__c = ‘Mumbai’;
    oppor.add(opp);
   }
  }
 }
 update oppor;
}
```
##### Trigger Scenario 2 : 

> Apex trigger to update account rating when the opportunity stage equals closed won
```java
trigger Opportunitystageclosedwon_updatesaccountrating on Opportunity(before insert) {
 list < account > accounts = new list < account > ();
 for (account acc: [select name, rating from account]) {
  for (opportunity opp: trigger.new) {
   if (opp.stagename == ‘closed won’) {
    acc.Rating = ‘hot’;
    accounts.add(acc);
   }
  }
 }
 update accounts;
}
```

##### Trigger Scenario 3 : 

> Prevent an account from deleting with trigger, if it has 2 or more contacts.

```java
trigger errordelete on Account(before delete) {
 set < id > acctid = new set < id > ();
 for (account a: trigger.old) {
  acctid.add(a.id);
 }
 map < id, account > accounts = new map < id, account > ([select id, name, (select lastname, firstname from contacts) from account where id in: acctid]);
 for (account acc: trigger.old) {
  if (accounts.get(acc.Id).contacts.size() >= 2 acc.adderror(‘account records cannot be deleted’);
  }
 }
}
```
##### Trigger Scenario 4 :

> When ever new account is created with annual revenue more than five lakhs then add wilson as account team member.

```java
trigger accountteammemberexample on Account(after insert) {
 list < accountteammember > actteams = new list < accountteammember > ();
 user u = [select id from user where alias = ’swethag’];
 for (account acc: trigger.new) {
  if (acc.annualrevenue > 500000) {
   accountteammember actteam = new accountteammember();
   actteam.UserId = u.id;
   actteam.AccountId = acc.Id;
   actteam.TeamMemberRole = ‘Account Manager’;
   actteams.add(actteam);
  }
 }
 insert actteams;
}
```
##### Trigger Scenario 5 : 

> When ever the Account is created with Industry as Banking then create a contact for account, Contact Lastname as Account name and contact phone as account phone.

```java
trigger accountafterhandler on Account(before insert) {
 list < contact > contacts = new list < contact > ();
 for (account acc: trigger.new) {
  if (acc.Industry == ‘Banking’) {
   contact con = new contact();
   con.LastName = acc.name;
   con.Phone = acc.Phone;
   con.AccountId = acc.Id;
   contacts.add(con);
  }
 }
 insert contacts;
}
```
##### Trigger Scenario 6 : 

> When ever a case is created with origin as email then set status as new and Priority as Normal

```java
trigger caseorigin on Case(before insert) {
 for (
  case c: trigger.new) {
  if (c.origin == ‘Email’) {
   c.status = ‘New’;
   c.priority = ‘Medium’;
  }
 }
}
```
##### Trigger Scenario 7 : 

> When ever lead is created with leadSource as Web then give rating as cold otherwise hot

```java
trigger leadexample on Lead(before insert) {
 for (lead ld: trigger.new) {
  if (ld.leadsource == ‘web’) {
   ld.Rating = ‘cold’;
  } else {
   ld.Rating = ‘hot’;
  }
 }
}
```
##### Trigger Scenario 8 : 

> whenever a account phone is modified/updated then contact records with phone fields(otherphone with oldvalue and homephone with newvalue) associated with this account record gets updated as same as account phone field.

```java
trigger accountupdatedexample on Account(before update) {
 map < id, account > accold = trigger.oldmap;
 map < id, account > accnew = trigger.newmap;
 set < id > keys = accold.keySet();
 list < id > myid = new list < id > ();
 for (ID K: keys) {
  account oldvalues = accold.get(K);
  account newvalues = accnew.get(K);
  if (oldvalues.phone != newvalues.phone) {
   myid.add(K);
  }
 }
 for (contact con: [select lastname, phone from contact where accountid in: myid]) {
  account a = accold.get(con.accountid);
  account b = accnew.get(con.accountid);
  con.otherphone = a.phone;
  con.homephone = b.phone;
 }
}
```
##### Trigger Scenario 9 : 

> When ever opportunity stagename is modifed to closed won then set closedate as today and type as new Customer.

```java
trigger opporupdate on Opportunity(before update) {
 Map < Id, Opportunity > oppold = Trigger.oldMap;
 Map < Id, Opportunity > oppnew = Trigger.newMap;
 Set < Id > keys = oppold.keySet();
 for (Id rid: keys) {
  Opportunity oldopt = oppold.get(rid);
  Opportunity newOpt = oppnew.get(rid);
  if (oldopt.stagename != ’Closed won’ && newOpt.stagename == ’Closed won’) {
   newOpt.closeDate = System.today();
   newOpt.type = ’New Customer’;
  }
 }
}
```
##### Trigger Scenario 10 : 

> when a new opportunity is created with amount more than 50 lakhs add the wilson as opportunityteamMember.

```java
trigger optamountteammember on Opportunity(before insert) {
 list < OpportunityTeamMember > teams = new list < OpportunityTeamMember > ();
 user u = [select id from user where alias = ’schar’];
 for (opportunity opt: trigger.new) {
  if (opt.amount > 5000000) {
   OpportunityTeamMember atm = new OpportunityTeamMember();
   atm.OpportunityId = opt.id;
   atm.UserId = u.id;
   atm.TeamMemberRole = ‘Opportunity Manager’;
   atm.OpportunityAccessLevel = ‘All’;
   teams.add(atm);
  }
 }
 insert teams;
}
```
##### Trigger Scenario 11 : 

> whenever the stagename is modified to closedwon then set the closedate as today

apex class:
```java
public class optytriggerhandler {
 public void udpate(map < id, opportunity > oldmap, map < id, opportunity > newmap) {
  for (id keys: oldmap.keySet()) {
   opportunity oppold = oldmap.get(keys);
   opportunity oppnew = newmap.get(keys);
   if (oppold.Stagename != ‘closed won’ && oppnew.Stagename == ‘closed won’) {
    oppnew.CloseDate = system.today();
   }
  }
 }
}
```
trigger class:

```java
trigger OpportunityUpdate on Opportunity (before update) {
    optytriggerhandler.udpate(trigger.oldmap, trigger.newmap);
}
```
##### Trigger Scenario 12 :

> when a new lead is created with leadsource as web then make the other user as the owner of the record.

```java
trigger leadownerassignment on Lead(before insert) {
 for (lead ld: trigger.new) {
  if (ld.leadsource == ‘web’) {
   user u = [select id from user where alias = ‘swethag’];
   ld.ownerid = u.id;
  }
 }
}
```
##### Trigger Scenario 13 : 

> when a new contact is created for a existing account then set contact otherphone as account phone.

```java
trigger contactaccountrelation on Contact(before insert) {
 list < account > acc = [select id, name from account];
 for (account a: acc) {
  for (contact con: [select lastname, accountid from contact where accountid =: a.id]) {
   con.OtherPhone = a.Phone;
  }
 }
}
```
##### Trigger Scenario 14 : 

> Create “Top X Designation” custom object which is the related list to Opportunity (Look up Relationship). In the Top X Designation object, create the fields Type (Picklist), Document Attached (Checkbox) Create one field Handoff Attached with pick list type with values are Yes, No on Opportunity Object. Logic :- If Type (Top X Designation) = “Contract Flow Down/Handoff”, and “Document Attached” = True then “Handoff Attached” = True, otherwise false.

```java
trigger updateHandoffattachedupdateHandoffattached on Top_X_Designation__c(after insert, after update, after delete) {
 map < id, Top_X_Designation__c > maptop = new map < id, Top_X_Designation__c > ();
 set < id > oppid = new set < id > ();
 map < id, Top_X_Designation__c > maptopfalse = new map < id, Top_X_Designation__c > ();
 set < id > oppidfalse = new set < id > ();
 list < opportunity > opplist = new list < opportunity > ();
 list < opportunity > opptoupdate = new list < opportunity > ();
 opportunity opp = new opportunity();
 if (trigger.isafter) {
  if (trigger.isinsert || trigger.isupdate) {
   for (Top_X_Designation__c top: trigger.new) {
    if (top.Document_Attached__c == true && ((top.Type__c == ’Contract Flow Down’) || (top.Type__c == ’Handoff’))) {
     maptop.put(top.OppLookUp__c, top);
     oppid.add(top.OppLookUp__c);
    } else {
     maptopfalse.put(top.OppLookUp__c, top);
     oppidfalse.add(top.OppLookUp__c);
    }
   }
   opplist = [select stagename, handoff_attached__c from opportunity where ID in: oppid and ID in: oppidfalse];
   for (opportunity opp: opplist) {
    if (maptop.containsKey(opp.Id)) {
     opp.Handoff_Attached__c = ‘yes’;
     opptoupdate.add(opp);
    }
    if (maptopfalse.containsKey(opp.id)) {
     opp.Handoff_Attached__c = ’no’;
     opptoupdate.add(opp);
    }
   }
   if (opptoupdate.size() > 0 update opptoupdate;
   }
  }
 }
}
```
##### Trigger Scenario 15 : 

> The following Trigger will fires when we try to create the account with same name i.e. Preventing the users to create Duplicate Accounts

```java
trigger AccountDuplicateTrigger on Account(before insert, before update) {
 map < string, account > accountmap = new map < string, account > ();
 for (account acc: trigger.new) {
  if ((acc.name != null) && (trigger.isinsert || (acc.name != trigger.oldmap.get(acc.id).name))) {
   if (accountmap.containsKey(acc.Name)) {
    acc.name.adderror(‘account name already exists’);
   } else {
    accountmap.put(acc.Name, acc);
   }
  }
 }
 for (account account: [select id, name from account where name IN: accountmap.keyset()]) {
  account accountnewmap = accountmap.get(account.name);
  accountnewmap.Name.adderror(‘account with this name already exixts’);
 }
}
```
##### Trigger Scenario 16 : 

> Trigger to count the total number of contact records associated with that Account

```java
trigger Accountcountcontacts on Contact(after insert, after update, after delete, after undelete) {
 set < id > acctids = new set < id > ();
 list < contact > contacts = trigger.isdelete ? trigger.old : trigger.new;
 for (contact con: contacts) {
  if (con.accountid != null) {
   acctids.add(con.accountid);
  }
 }
 list < account > listaccounts = new list < account > ();
 for (aggregateresult ar: [select accountid accid, count(id) contactcount from contact where accountid in: acctids group by accountid]) {
  account a = new account();
  a.id = (id) ar.get(‘accid’);
  a.Number_of_Contacts__c = (decimal) ar.get(‘contactcount’);
  listaccounts.add(a);
 }
 update listaccounts;
}
```
##### Trigger Scenario 17:- 

> Create the object called “Customer” and create the Master-Detail Relationship on Customer object so that Customer will be the related list to Account record. Create the field called “Account Manager” on Customer object which is lookup to the user object. Now Logic is when we create Customer record for account record, then the user in Account Manager field will be automatically added to Account Team of that associated account.

```java
trigger InsertAccountTeam on Customer__c(after insert) {
  list < accountteammember > atmlist = new list < accountteammember > ();
  accountteammember atm = new accountteammember();
  list < accountshare > newshare = new list < accountshare > ();
  if (trigger.isinsert) {
   for (Customer__c c: trigger.new) {
    if (c.Account_Manager__c != null) {
     atm = new accountteammember();
     atm.accountid = c.CustomerAccount__c;
     atm.teammemberrole = ‘Account Manager’;
     atm.userid = c.Account_Manager__c;
     AccountShare shares = new AccountShare();
     shares.AccountId = c.CustomerAccount__c;
     shares.AccountAccessLevel = ‘Read / Write’;
     shares.UserOrGroupId = c.Account_Manager__c;
     shares.OpportunityAccessLevel = ‘Read Only’;
     shares.CaseAccessLevel = ’Read Only’;
     newshare.add(shares);
     atmlist.add(atm);
    }
   }
   if (atmlist != null)
    insert atmlist;
   if (newshare != null && newshare.size() > 0 list < database.SaveResult > sr = database.insert(newshare, false);
   }
  }
}
```
##### Trigger Scenario 18 : 
> 1. Set the OWD on the opportunity as private 
> 2. Remove modify all permission on wilson’s profile 
> 3. whenever the opportunity stagename is modified to closed won and amount is more than 10 lacs share the record with wilson

Apex Class:
```java
public class afterupdateopptrigg {
 public static void afterupdate(map < id, opportunity > oldmap, map < id, opportunity > newmap) {
  list < opportunityshare > share = new list < opportunityshare > ();
  user u = [select id from user where alias = ‘swethag’];
  for (id key: oldmap.keyset()) {
   opportunity oldopt = oldmap.get(key);
   opportunity newopt = newmap.get(key);
   if (oldopt.stagename != ’closed won’ && newopt.StageName == ‘closed won’) {
    if (newopt.Amount > 1000000) {
     opportunityshare os = new opportunityshare();
     os.OpportunityId = key;
     os.UserOrGroupId = u.id;
     os.OpportunityAccessLevel = ‘read’;
     os.RowCause = ‘manual’;
     share.add(os);
    }
   }
  }
  insert share;
 }
}
```
Trigger Class:

```java
trigger afterupdateopptrigg on Opportunity (after update) {
    afterupdateopptrigg.afterupdate(trigger.old, trigger.new);
}
```
##### Trigger Scenario 19 : 

> When a new Account record is inserted verify the industry field value, if industry field value is Education then assign the owner as karthik.

Trigger :

```java
trigger accountowner on Account(before insert) {
 User u = [select id from User where username = ’developer @batch0267.com’];
 for (Account a: Trigger.New) {
  if (a.Industry == ’Education’) {
   a.ownerId = u.id;
  }
 }
}
```
TestClass :
```java
@isTest
private class AccountOwnerTest {
 @isTest
 static void testme() {
  User u = [select id from User where username = ’developer @batch0267.com’];
  Integer count = [select count() from Account];
  Account a = new Account(Name = ’Test’, Industry = ’Education’);
  try {
   insert a;
  } catch (Exception e) {
   System.debug(e);
  }
  Integer size = [select count() from Account];
  System.assertEquals(size, count + 1);
  Account acc = [select id, ownerId from Account where id =: a.id];
  System.assertEquals(acc.ownerId, u.id);
 }
}
```
##### Trigger Scenario 20 :

> When ever we are trying to delete the account record which has a contacts for it .,Then deletion has to fail

Trigger :

```java
trigger accountDelete on Account(before Delete) {
  List < Account > accsList = [select id, name, (select id from Contacts) from Account where id in: Trigger.Old];
  for (Account a: accsList) {
   if (a.contacts.size() > 0 a.addError(‘We cannot delete this account’);
   }
  }
```
TestClass :
```java
@isTest
private class accountDeleteTest {
 @isTest
 static void testMe() {
  Account a1 = new Account(Name = ’Test’);
  insert a1;
  Contact c1 = new Contact(LastName = ’test’, accountId = a1.id);
  insert c1;
  Account a2 = new Account(name = ’Demo’);
  insert a2;
  Integer myCount = [select count() from Account];
  try {
   delete a1;
  } catch (Exception e) {
   System.debug(e);
  }
  Integer count = [select count() from Account];
  System.assertEquals(count, mycount);
  try {
   delete a2;
  } catch (Exception e) {
   System.debug(e);
  }
  Integer size = [select count() from Account];
  System.assertEquals(size, mycount - 1);
 }
}
```
##### Trigger Scenario 21 : 

> When ever account record is deleted successfully send the email confirmation alert to the Account email address.

Trigger :

```java
trigger accountDeleteEmail on Account(after Delete) {
  List < Messaging.Email > emails = new List < Messaging.Email > ();
  for (Account a: Trigger.Old) {
   Messaging.SingleEmailMessage email1 = new Messaging.SingleEmailMessage();
   email1.setToAddresses(new String[] {
    a.email__c
   });
   email1.setSubject(‘Account Deleted’);
   String body = '<h1> Dear Cusomer<h1><p >Your Account with Accoutn Id:' + a.id;
   body = body + ’is successfully deleted < /p><br/ > < p > Thanks < br / > SalesTeam < /p>’;email1.setHtmlBody(body);emails.add(email1);}
   Messaging.sendEmail(emails);
  }
```
##### Trigger Scenario 22 : 

> 1. Create two objects credit card and Application . 
> 2. Create lookup field on Application choosing Credit card as parent
> 3. When we delete any credit card record all corresponding application records also be deleted.

```java
trigger creditcardDeletion on CreditCard__c(before delete) {
 List < Id > cards = new List < Id > ();
 for (CreditCard__c c: Trigger.Old) {
  if (c.card_Type__c == ’Platinum’) {
   cards.add(c.id);
  }
 }
 List < Application__c > apps = [select id from Application__c where CreditCard__c in: cards];
 delete apps;
}
```
#### Trigger Scenario 23:

> Create “Top X Designation” custom object which is the related list to
> Opportunity (Look up Relationship). In the Top X Designation object,
> create the fields
> -   Type (Picklist),
> -   Document Attached (Checkbox) Create one field Handoff Attached with pick list type with values are Yes, No on Opportunity Object.
> 
> **Logic :-** If Type (Top X Designation) = "Contract Flow Down/Handoff", and "Document Attached” = True then "Handoff Attached"
> = True, otherwise false.

```
trigger updateHandoffattached on Top_X_Designation__c(after insert, after update, after delete) {
 map maptop = new map();
 set oppid = new set();
 map maptopfalse = new map();
 set oppIdInFalseCase = new set();
 map deletecase = new map();
 set delcaseid = new set();
 list opplist = new list();
 list listtoupdate = new list();
 for (Top_X_Designation__c top: trigger.isdelete ? trigger.old : trigger.new) {
  if (trigger.isInsert || trigger.isUpdate) {
   if (top.Document_Attached__c == true && ((top.Type__c == ’Contract Flow Down’) || (top.Type__c == ’Handoff’))) {
    maptop.put(top.Opp_top_x_designation__c, top.Id);
    oppid.add(top.Opp_top_x_designation__c);
    system.debug(‘here‘ + top.Opp_top_x_designation__c);
   } else
    maptopfalse.put(top.Opp_top_x_designation__c, top.id);
   oppid.add(top.Opp_top_x_designation__c);
   system.debug(‘here in
    else part‘ + top.Opp_top_x_designation__c);
  }
  if (trigger.isDelete) {
   deletecase.put(top.Opp_top_x_designation__c, top.Id);
   delcaseid.add(top.Opp_top_x_designation__c);
   oppid.add(top.Opp_top_x_designation__c);
  }
 }
 opplist = [select Id, Handoff_Attached__c from opportunity where id in: oppid];
 for (opportunity opp: opplist) {
  if (maptop.containsKey(opp.id)) {
   opp.Handoff_Attached__c = ’yes’;
  }
  if (maptopfalse.containsKey(opp.id)) {
   opp.Handoff_Attached__c = ’no’;
  }
  if (delcaseid.contains(opp.Id)) {
   opp.Handoff_Attached__c = ”;
  }
  listtoupdate.add(opp);
 }
 if (listtoupdate.size() > 0) {
  update listtoupdate;
  system.debug(‘testL’ + listtoupdate);
 }
}
```
and its test class
```
@istest
public class testforhandoff {
 @istest static void methodhandoff() {
  opportunity opp = new opportunity();
  opp.Name = ’test opp’;
  opp.StageName = ’Needs Analysis’;
  opp.CloseDate = system.today();
  insert opp;
  Top_X_Designation__c tx = new Top_X_Designation__c();
  tx.Opp_top_x_designation__c = opp.Id;
  tx.Document_Attached__c = true;
  tx.Type__c = ‘Handoff’;
  insert tx;
 }
 @istest
 static void testmethod1() {
  opportunity opp1 = new opportunity();
  opp1.Name = ’test opp’;
  opp1.StageName = ’Needs Analysis’;
  opp1.CloseDate = system.today();
  insert opp1;
  Top_X_Designation__c tx1 = new Top_X_Designation__c();
  tx1.Opp_top_x_designation__c = opp1.id;
  tx1.Document_Attached__c = false;
  tx1.Type__c = ’Handoff’;
  insert tx1;
 }
 @istest
 static void deletemethod() {
  opportunity opp1 = new opportunity();
  opp1.Name = ’test opp’;
  opp1.StageName = ’Needs Analysis’;
  opp1.CloseDate = system.today();
  insert opp1;
  Top_X_Designation__c tx1 = new Top_X_Designation__c();
  tx1.Opp_top_x_designation__c = opp1.id;
  tx1.Document_Attached__c = false;
  tx1.Type__c = ’Handoff’;
  insert tx1;
  delete tx1;
 }
}
```

### Trigger Scenario 24:-

> The following Trigger will fires when we try to create the account
> with same name i.e. Preventing the users to create Duplicate Accounts
```
trigger AccountDuplicateTrigger on Account(before insert, before update) {
 for (Account a: Trigger.new) {
  List < Account > acc = “Select id from Account where Name =: a.Name and Rating =: a.Rating“;
  if (acc.size() > 0) {
   acc.Name.addError('You Cannot Create the Duplicate Account');
  }
 }
}
```
### Trigger Scenario 25:-

> The following trigger updates the field called “Hello” by the value
> “World” whenever we are creating an account or updating an account
> record. Create the field called “Hello” on the Account Object (Data
> Type = Text)
``` java
trigger HelloWorld on Account(before insert, before update) {
 List < Account > accs = Trigger.new;
 MyHelloWorld my = new MyHelloWorld();
 my.addHelloWorld(accs);
}
```
**Class:**
``` java
public class MyHelloWorld {
 public void addHelloWorld(List < Account > accs) {
  for (Account a: accs) {
   if (a.Hello__c != 'World') {
    a.Hello__c = 'World';
   }
  }
 }
}
```
### Trigger Scenario 26:-

> The following trigger describes when the leads are inserted into the
> data base it would add Doctor prefixed for all lead names. This is
> applicable for both inserting and updating the lead records.
``` java
trigger PrefixDoctor on Lead(before insert, before update) {
 List < Lead > leadList = trigger.new;
 for (Lead l: leadList) {
  l.firstname = 'Dr.' + l.firstname;
 }
}
```
### Trigger Scenario 27:-

> The following trigger adds the Opportunity Owner into the sales team
> automatically whenever the Opportunity is created.
``` java
trigger OppTeam on Opportunity(after insert) {
 List < OpportunityShare > sharesToCreate = new List < OpportunityShare > ();
 List < OpportunityTeamMember > oppteam = new List < OpportunityTeamMember > ();
 OpportunityShare oshare = new OpportunityShare();
 oshare.OpportunityAccessLevel = 'Edit';
 oshare.OpportunityId = trigger.new” 0”.Id;
 oshare.UserOrGroupId = trigger.new” 0”.createdbyid;
 sharesToCreate.add(oshare);
 OpportunityTeamMember ot = new OpportunityTeamMember();
 ot.OpportunityId = trigger.new” 0”.Id;
 ot.UserId = trigger.new” 0”.OwnerId;
 ot.TeamMemberRole = 'Account Manager';
 oppteam.add(ot);
 if (Oppteam != null && Oppteam.size() > 0) {
  insert Oppteam;
 }
 if (sharesToCreate != null && sharesToCreate.size() > 0) {
  list < Database.SaveResult > sr = Database.insert(sharesToCreate, false);
 }
}
```
### Trigger Scenario 28:-

> Create the object called “Books” and create field “Price”(data type is
> Currency) under this object. Whenever we enter some amount of money in the Price field and once we
> click on save button, the value we entered in the Price field is 10% less than the
> actual price. This is applicable for while both inserting and updating records.
``` java 
trigger DiscountTrigger on Book__c(before insert, before update) {
 List < Book__c > books = Trigger.new;
 PriceDiscount.applyDiscount(books);
}
```

**Class**
``` java
public class PriceDiscount {
 public static void applyDiscount(List < Book__c > books) {
  for (Book__c b: books) {
   b.Price__c *= 0.9;
  }
 }
}
```
### Trigger Scenario 29:-

> The following trigger will prevent the users from deleting the
> Accounts. This is because System Administrator has all the
> permissions, we cannot change the permissions.
``` java
trigger AccountDelete on Account(before delete) {
 for (Account Acc: trigger.old) {
  acc.adderror('You Cannot Delete the Account Record');
 }
}
```
### Trigger Scenario 30:-

> Create Custom field called “Number of Locations” on the Account Object
> (Data Type=Number) The following trigger creates the number of contacts which are equal
> to the number which we will enter in the Number of Locations field on
> the Account Object

**Code:**
``` java
trigger ContactsCreation on Account(after insert) {
 list < contact > listContact = new list < contact > ();
 map < id, decimal > mapAcc = new map < id, decimal > ();
 for (Account acc: trigger.new) {
  mapAcc.put(acc.id, acc.Number_of_Locations__c);
 }
 if (mapAcc.size() > 0 && mapAcc != null) {
  for (Id accId: mapAcc.keyset()) {
   for (integer i = 0; i < mapAcc.get(accId); i++) {
    contact newContact = new contact();
    newContact.accountid = accId;
    newContact.lastname = 'contact' + i;
    listContact.add(newContact);
   }
  }
 }
 if (listContact.size() > 0 && listContact != null)
  insert listContact;
}
```
### Trigger Scenario 31:-

> Create the object called “Customer Project” and create Status field
> under this object with picklist data type (Values=Active, Inactive).
> Create the relationship between this custom object and Opportunity so
> that Customer Project is related list to the Opportunity. Create 
> Active Customer project‑ field on Opportunity object (Data
> Type=Checkbox) The logic is when we create Customer Project for an
> Opportunity with the Status=Active, then  Active Customer project
> check box on the Opportunity Detail page is automatically checked.

**Code:-**
``` java
trigger UpdateCPActivenOnOppty on Customer_Project__c(after insert) {
 List < Opportunity > opps = new List < Opportunity > ();
 for (Customer_Project__c cp: Trigger.New) {
  if (cp.Status__c == 'Active') {
   Opportunity opp = new Opportunity(id = cp.Opportunity__c);
   opp.Active_Customer_Project__c = True;
   opps.add(opp);
  }
 }
 update opps;
}
```
### Trigger Scenario 32:-

**Description:**

> We have Stakeholder object that is the related list to Opportunity and
> Contact. On Contact detail page, we have NPS ID field on the Contact
> detail page that is look up to the Voice of NPS object (Customer
> Object). The following code will get the NPS ID field value from the
> Contact detail page in the Stackholders page which we can able to
> clickable.
> 
> Create NPS Id 1 field on the stackholders object which is the look up
> to the Voice of NPS object (Customer Object)
> 
> **Note:-**
> 
> ‑ we can also get the NPS Id 1 on the Stakeholder’s page using the
> formula field but will not able to clickable.

**Code Single Record:-**
``` java
triggerUpdateNPSid on Stakeholder__c(before insert, before update) {
 List < id > ConList = new List < id > ();
 for (Stakeholder__csh: Trigger.New) {
  ConList.add(sh.Contact_Name__c);
 }
 List < Contact > lc = “Select NPS_Id__c From Contact Where id IN: ConList”;
 for (Stakeholder__csh: Trigger.New) {
  sh.NPS_Id1__c = lc” 0”.NPS_Id__c;
 }
}
```
**Code Bulk Records:-**
``` java
triggerUpdateSalesRep on Account(Before insert, Before Update) {
 Set < Id > setAccOwner = new Set < Id > ();
 for (Account Acc: trigger.new) {
  setAccOwner.add(Acc.OwnerId);
 }
 Map < Id, User > User_map = new Map < Id, User > (“select Name from User where id in: setAccOwner”);
 for (Account Acc: Trigger.new) {
  User usr = User_map.get(Acc.OwnerId);
  Acc.sales_Rep1__c = usr.Name;
 }
}
```
### Trigger Scenario 33:-

> Create “Sales Rep” field with data type (Text) on the Account Object.
> When we create the Account record, the Account Owner will be
> automatically added to Sales Rep field. When we update the Account
> owner of the record, then also the Sales Rep will be automatically
> updated.
``` java
triggerUpdateSalesRep on Account(Before insert, Before Update) {
 Set < Id > setAccOwner = new Set < Id > ();
 for (Account Acc: trigger.new) {
  setAccOwner.add(Acc.OwnerId);
 }
 Map < Id, User > User_map = new Map < Id, User > (“select Name from User where id in: setAccOwner”);
 for (Account Acc: Trigger.new) {
  User usr = User_map.get(Acc.OwnerId);
  Acc.sales_Rep1__c = usr.Name;
 }
}
```
### Trigger Scenario 34:-

> Create the field called “Contact Relationship” checkbox on the Contact
> Object and Create the related object called “Contact Relationship”
> which is related list to the Contacts.(Look Up Relationship).
> 
> Now logic is when we create contact by checking Contact Relationship
> checkbox, then Contact Relationship will be created automatically for
> that contact.

**Code:**
``` java
trigger CreateCRonContactCreation on Contact(after insert) {
 if (trigger.isAfter) {
  if (trigger.isInsert) {
   ContactMasterHandler ConIns = New ContactMasterHandler();
   ConIns.createContactRelationshipByContact(trigger.New);
  }
 }
}
```
**Class:**
``` java
Public Class ContactMasterHandler {
 public void createContactRelationshipByContact(list < Contact > List_Contacts) {
  list < Contact_Relationship__c > ConList = new list < Contact_Relationship__c > ();
  for (Contact newconts: List_Contacts) {
   if (newconts.Contact_Relationship__c == true) {
    Contact_Relationship__c CR = new Contact_Relationship__c();
    CR.Name = newconts.Lastname;
    CR.Contact__c = newconts.id;
    ConList.add(CR);
   }
  }
  insert ConList;
 }
```
### Trigger Scenario 35:-

> When we change the Owner of the Contact Relationship, then the Owner
> name will be automatically populated in the Contact Relationship Name
> field.

**Trigger:**
``` java
trigger ContactRelationshipMasterTrigger on Contact_Relationship__c(before update) {
 if (trigger.isBefore) {
  if (trigger.isUpdate) {
   updateCROwnerName ConRelUpd = New updateCROwnerName();
   ConRelUpd.updateContactRelationshipNameByOwner(trigger.New);
  }
 }
}
```
**Class:**
``` java
Public Class updateCROwnerName {
 public void updateContactRelationshipNameByOwner(list < Contact_Relationship__c > co nt_Rel) {
  map < Id, Id > map_Id_Own = new map < id, id > ();
  map < Id, string > map_id_Name = new map < id, string > ();
  set < id > Idset = new set < id > ();
  for (Contact_Relationship__c List_recs: cont_Rel) {
   Idset.add(List_recs.Ownerid);
  }
  list < user > u = “select id, Name from user where id in: Idset”;
  for (user list_users: u) {
   map_id_Name.put(list_users.Id, list_users.Name);
  }
  if (u != null && u.size() > 0) {
   for (Contact_Relationship__c List_recs: cont_Rel) {
    if (List_recs.Ownerid != null) {
     List_recs.Name = map_id_Name.get(List_recs.Ownerid);
    }
   }
  }
 }
}
```
### Trigger Scenario 36:-

> Create the field called “Contact Relationship” checkbox on the Contact
> Object and Create the object called “Contact Relationship” which is
> related list to the Contacts.(Look Up Relationship).
> 
> Trigger Scenario 12 logic will says that when we create contact by
> checking Contact Relationship checkbox, then Contact Relationship will
> be created automatically for that contact.
> 
> No this logic will for when we delete the Contact, Then Contact
> Relationship will be deleted automatically
``` java
trigger DeleteCRonContactDeletion on Contact(before delete) {
 if (trigger.isBefore) {
  if (trigger.isDelete)
   for (Contact c: Trigger.old) {
    Contact_relationship__c CR = new Contact_relationship__c();
    CR = “Select Id from Contact_Relationship__c where Contact__c IN: GlobalUtility.getUni
    queIds(Trigger.Old)”;
    delete CR;
   }
 }
}
```
**Global Utility Classs:**
``` java
public static set < Id > getUniqueIds(list < SObject > sobs) {
 set < Id > Ids = new set < Id > ();
 for (SObject sob: sobs) {
  Ids.add(sob.Id);
 }
 return Ids;
}
```
### Trigger Scenario 37:-

> Create the field called “Contact Relationship” checkbox on the Contact
> Object and Create the object called “Contact Relationship” which is
> related list to the Contacts.(Look Up Relationship).
> 
> Trigger Scenario 14 will says that when we delete the Contact, Then
> Contact Relationship will be deleted automatically
> 
> Now the Logic is when we undelete the Contact, then Contact
> Relationship will be undeleted automatically

**Triggser:**
``` java
trigger UnDeleteCRonContactUnDeletion on Contact(After Undelete) {
 if (trigger.isUndelete) {
  ContactMasterHandler_Undelete ConIns = New ContactMasterHandler_Undelete();
  ConIns.undeleteContactRelationshipsByContact(Trigger.New);
 }
}
```
**Class**
``` java
Public Class ContactMasterHandler_Undelete {
 public void undeleteContactRelationshipsByContact(list < Contact > List_Contacts) {
  set < Id > ContactIds = New set < Id > ();
  if (List_Contacts != null && List_Contacts.size() > 0) {
   list < Contact_Relationship__c > List_ConRels = new list < Contact_Relationship__c > ();
   List_ConRels = “select id from Contact_Relationship__c where isDeleted = TRUE and Co
   ntact__c in: GlobalUtility.getUniqueIds(List_Contacts)”;
   undelete List_ConRels;
  }
 }
}
```
### Trigger Scenario 38:-

> Create field called “Count of Contacts” on Account Object. When we add
> the Contacts for that Account then count will populate in the field on
> Account details page. When we delete the Contacts for that Account,
> then Count will update automatically
> 
> **Note:**
> 
> The above logic will be applicable when we have look up relationship.
> But When we have the Master – Detail relationship, then we can create
> Rollup Summary field to get the count of child records using “Count”
> function.
``` java
trigger CountOfContacts on Contact(after insert, after delete) {
 set < id > accid = new set < id > ();
 list < contact > contactlist = new list < contact > ();
 list < contact > listcon = new list < contact > ();
 list < account > acclist = new list < account > ();
 list < account > listAcc = new list < account > ();
 map < id, integer > mapCount = new map < id, integer > ();
 if (trigger.isinsert) {
  for (contact con: trigger.new) {
   accid.add(con.accountid);
  }
 }
 if (trigger.isdelete) {
  for (contact con: trigger.old) {
   accid.add(con.accountid);
  }
 }
 acclist = “select id, name from account where id in: accid”;
 contactlist = “select id, name, accountid from contact where accountid in: accid”;
 for (account acc: acclist) {
  listcon.clear();
  for (contact c: contactlist) {
   if (c.accountid == acc.id) {
    listcon.add(c);
    mapCount.put(c.accountid, listcon.size());
   }
  }
 }
 if (acclist.size() > 0) {
  for (Account a: acclist) {
   if (mapCount.get(a.id) == null)
    a.Count_Of_Contacts__c = 0;
   else
    a.Count_Of_Contacts__c = mapCount.get(a.id);
   listAcc.add(a);
  }
 }
 if (listAcc.size() > 0)
  update listAcc;
}
```
### Trigger Scenario 39:-

> Create the object called “Customer” and create the Master-Detail
> Relationship on Customer object so that Customer will be the related
> list to Account record. Create the field called “Account Manager” on
> Customer object which is lookup to the user object.
> 
> Now Logic is when we create Customer record for account record, then
> the user in Account Manager field will be automatically added to
> Account Team of that associated account.

**Code:**
``` java
trigger InsertAccountTeam on Customer__c(after insert) {
 List < AccountTeamMember > atm_list = new List < AccountTeamMember > ();
 AccountTeamMember atm = new AccountTeamMember();
 List < AccountShare > newShare = new List < AccountShare > ();
 if (trigger.isInsert) {
  For(Customer__c c: Trigger.new) {
   if (c.Account_Manager__c != null) {
    atm = new AccountTeamMember();
    atm.accountid = c.Account__c;
    atm.teamMemberRole = 'Account Manager';
    atm.UserId = c.Account_Manager__c;
    AccountShare shares = new AccountShare();
    shares.AccountId = c.Account__c;
    shares.UserOrGroupId = c.Account_Manager__c;
    shares.AccountAccessLevel = 'Read/Write';
    shares.OpportunityAccessLevel = 'Read Only';
    shares.CaseAccessLevel = 'Read Only';
    newShare.add(shares);
    atm_list.add(atm);
   }
  }
  if (atm_list != null)
   insert atm_list;
  if (newShare != null && newShare.size() > 0)
   List < Database.saveresult > sr = Database.insert(newShare, false);
 }
}
```
### Trigger Scenario 40:-

> The above trigger(Trigger Scenario 17) Logic is when we create
> Customer record for account record, then the user in Account Manager
> field will be automatically added to Account Team of that associated
> account.
> 
> Now the following trigger logic is when we update the user in the
> “Account Manager”, the Account team will be updated automatically.

**Code:**
``` java
trigger UpdateAccountTeam on Customer__c(before update) {
	List < AccountTeamMember > atm_list = new List < AccountTeamMember > ();
	AccountTeamMember atm = new AccountTeamMember();
	List < AccountShare > newShare = new List < AccountShare > ();
	if (trigger.isupdate) {
		if (trigger.isbefore) {
			Set < Id > setAccIds1 = new Set < Id > ();
			Set < Id > setDelATM = new Set < Id > ();
			Map < id,
			Set < Id >> mapAccMgrs = new Map < id,
			Set < Id >> ();
			for (Customer__c c: Trigger.new) {
				if (trigger.oldmap.get(c.Id).Account_Manager__c != c.Account_Manager__c && c.Account_Manager__c != null) {
					setAccIds1.add(c.Account__c);
				}
			}
			List < Customer__c > listPLAccMgrs = "select id,Account_Manager__c,Account__c from Customer__c where Account__c in:setAccIds1 and id not in:trigger.newmap.keyset()";
			if (listPLAccMgrs != null && listPLAccMgrs.size() > 0) {
				for (Customer__c c: listPLAccMgrs) {
					Set < Id > idMgrs = mapAccMgrs.get(c.Account__c);
					if (null == idMgrs) {
						idMgrs = new set < Id > ();
						mapAccMgrs.put(c.Account__c, idMgrs);
					}
					idMgrs.add(c.Account_Manager__c);
				}
			}
			Map < id,
			List < AccountTeamMember >> mapStdAccTeam = new Map < id,
			List < AccountTeamMember >> ();
			List < AccountTeamMember > listStdAcc Team = "select id,UserId,AccountId from AccountTeamMember where AccountId in:setAccIds1";
			if (listStdAccTeam != null && listStdAccTeam.size() > 0) {
				for (AccountTeamMember recAccTeam: listStdAccTeam) {
					List < AccountTeamMember > listStdAccTeamMap = mapStdAccTeam.get(recAccTeam.AccountId);
					if (null == listStdAccTeamMap) {
						listStdAccTeamMap = new List < AccountTeamMember > ();
						mapStdAccTeam.put(recAccTeam.AccountId, listStdAccTeamMap);
					}
					listStdAccTeamMap.add(recAccTeam);
				}
			}
			system.debug('***********' + mapAccMgrs);
			for (Customer__c c: Trigger.new) {
				if (trigger.oldmap.get(c.Id).Account_Manager__c != c.Account_Manager__c && c.Account_Manager__c != null) {
					List < AccountTeamMember > listAccTeam = mapStdAccTeam.get(c.Account__c);
					Set < Id > idMgrs = mapAccMgrs.get(c.Account__c);
					if (listAccTeam != null && listAccTeam.size() > 0) {
						if (idMgrs != null && idMgrs.size() > 0 && !(idMgrs.Contains(trigger.oldmap.get(c.Id).Account_Manager__c))) {
							for (AccountTeamMember recATM: listAccTeam) {
								if (recATM.UserId == trigger.oldmap.get(c.Id).Account_Manager__c) {
									setDelATM.add(recATM.Id);
								}
							}
						}
						else if (idMgrs == null) {
							for (AccountTeamMember recATM: listAccTeam) {
								setDelATM.add(recATM.Id);
							}
						}
					}
					atm = new AccountTeamMember(accountid = c.Account__c, teamMemberRole = 'Account Manager', UserId = c.Account_Manager__c);
					AccountShare shares = new AccountShare();
					shares.AccountId = c.Account__c;
					shares.UserOrGroupId = c.Account_Manager__c;
					shares.AccountAccessLevel = 'Edit';
					shares.OpportunityAccessLevel = 'None';
					newShare.add(shares);
					atm_list.add(atm);
				}
			}
			List < AccountTeamMember > listDelATM = "select id from AccountTeamMember where id in:setDelATM";
			if (listDelATM != null && listDelATM.size() > 0) {
				delete listDelATM;
			}
			if (atm_list != null) {
				insert atm_list;
			}
			if (newShare != null && newShare.size() > 0) {
				List < Database.saveresult > sr = Database.insert(newShare, false);
			}
		}
	}
}
```
### Trigger Scenario 41:-

> The trigger scenario 17 Logic is when we create Customer record for
> account record, then the user in Account Manager field will be
> automatically added to Account Team of that associated account.
> 
> Now the following trigger gives the logic about when we delete the
> “Customer” of that account, then the user will deleted automatically
> from the Account Team of that account.
``` java
trigger DeleteAccountTeam on Customer__c(before delete) {
 List < AccountTeamMember > atm_list = new List < AccountTeamMember > ();
 AccountTeamMember atm = new AccountTeamMember();
 List < AccountShare > newShare = new List < AccountShare > ();
 if (trigger.isdelete) {
  set < id > setAccids = new set < id > ();
  Set < Id > setDelATM = new Set < Id > ();
  Map < id, Set < Id >> mapAccMgrs = new Map < id, Set < Id >> ();
  for (Customer__c c: Trigger.old) {
   setAccids.add(c.Account__c);
  }
  List < Customer__c > listPLAccMgrs = “select id, Account_Manager__c, Account__c from
  Customer__c where Account__c in: setAccids and id not in: trigger.oldmap.keyset()”;
  if (listPLAccMgrs != null && listPLAccMgrs.size() > 0) {
   for (Customer__c c: listPLAccMgrs) {
    Set < Id > idMgrs = mapAccMgrs.get(c.Account__c);
    if (null == idMgrs) {
     idMgrs = new set < Id > ();
     mapAccMgrs.put(c.Account__c, idMgrs);
    }
    idMgrs.add(c.Account_Manager__c);
   }
  }
  Map < id, List < AccountTeamMember >> mapStdAccTeam = new
  Map < id, List < AccountTeamMember >> ();
  List < AccountTeamMember > listStdAccTeam = “select id, UserId, AccountId from
  AccountTeamMember where AccountId in: setAccids”;
  if (listStdAccTeam != null && listStdAccTeam.size() > 0) {
   for (AccountTeamMember recAccTeam: listStdAccTeam) {
    List < AccountTeamMember > listStdAccTeamMap = mapStdAccTeam.get(recAccTeam.AccountId);
    if (null == listStdAccTeamMap) {
     listStdAccTeamMap = new List < AccountTeamMember > ();
     mapStdAccTeam.put(recAccTeam.AccountId, listStdAccTeamMap);
    }
    listStdAccTeamMap.add(recAccTeam);
   }
  }
  for (Customer__c c: Trigger.old) {
   List < AccountTeamMember > listAccTeam = mapStdAccTeam.get(c.Account__c);
   Set < Id > idMgrs = mapAccMgrs.get(c.Account__c);
   if (listAccTeam != null && listAccTeam.size() > 0) {
    if (idMgrs != null && idMgrs.size() > 0 && !(idMgrs.Contains(trigger.oldmap.get(c.Id).Account_Manager__c))) {
     for (AccountTeamMember recATM: listAccTeam) {
      if (recATM.UserId == trigger.oldmap.get(c.Id).Account_Manager__c)
       setDelATM.add(recATM.Id);
     }
    } else if (idMgrs == null) {
     for (AccountTeamMember recATM: listAccTeam)
      setDelATM.add(recATM.Id);
    }
   }
  }
  List < AccountTeamMember > listDelATM = “select id from AccountTeamMember
  where id in: setDelATM”;
  if (listDelATM != null && listDelATM.size() > 0)
   delete listDelATM;
 }
}
```
### Trigger Scenario 42:-

> When we create the Opportunity with the Probability=20, then the
> opportunity owner will be automatically added to Account Team of the
> associated account for that Opportunity.

**Code:**
``` java 
trigger UpdateATMwithOwneronOptyCreate on Opportunity(after insert, after update) {
  List < AccountShare > list_share = new List < AccountShare > ();
  List < AccountTeamMember > list_atm = new List < AccountTeamMember > ();
  for (Opportunity opp: Trigger.New) {
   if (opp.Probability == 20) {
    AccountTeamMember atm = new AccountTeamMember();
    atm.accountid = opp.accountid;
    atm.teamMemberRole = 'Account Manager';
    atm.UserId = opp.Ownerid;
    AccountShare share = new AccountShare();
    share.AccountId = opp.Accountid;
    share.UserOrGroupId = opp.OwnerId;
    share.AccountAccessLevel = 'Read/Write';
    share.OpportunityAccessLevel = 'Read Only';
    share.CaseAccessLevel = 'Read Only';
    list_atm.add(atm);
    list_share.add(share);
   }
  }
  if (list_atm != null)
   insert list_atm;
  if (list_share != null && list_share.size() > 0)
   List < Database.saveresult > sr = Database.insert(list_share, false);
```
**Technology**:

> In the above triggers
> 
> -   OpportunityTeamMember is backend name of Sales Team
> 
> -   For creation of Account Team we need to create Account Share which will bethere at the backend
> 
> -   For creation of Sales team, we need to create Opportunity Share which will be there at the backend
> 
> **Note**: For more clarity, go to the Data Loader and click on the “Export” operation and click on the select “Show all 
> [Salesforce](https://www.salesforce.com/in/?ir=1)  Objects” checkbox,
> then we can able to see Account Share and Opportunity Share.
