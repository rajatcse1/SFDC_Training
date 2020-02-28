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
