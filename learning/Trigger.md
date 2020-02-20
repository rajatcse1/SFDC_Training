## 1. Hallo world
   > - **Object**: Account
   > - **DML operation**: before insert, before update
   > - **Action**: This trigger will update custom field `Hello__c` of Account object with word `World` if it is not already
   ```java
      trigger HelloWorldTrigger on Account (before insert) {
         System.debug('Hello World!');
      }
   ```
   ```java
      trigger HelloWorld on Account(before insert, before update) {
         List < Account > accs = Trigger.new;
         for (Account a: accs) {
            if (a.Hello__c != 'World') {
               a.Hello__c = 'World';
            }
         }
      }
   ```
## 2. Trigger for automation:
   > Send mail on create/update object
   ```java
      trigger ExampleTrigger on Contact (after insert, after delete) {
         if (Trigger.isInsert) {
            Integer recordCount = Trigger.New.size();
            // Call a utility method from another class
            EmailManager.sendMail('Your email address', 'Trailhead Trigger Tutorial', 
                        recordCount + ' contact(s) were inserted.');
         }
         else if (Trigger.isDelete) {
            // Process after delete
         }
      }

      // to test open console and run
      Contact c = new Contact(LastName='Test Contact');
      insert c;
   ```
   > Add prefix on create/update object
   ```java
      trigger PrefixDoctor on Lead(before insert, before update) {
         List < Lead > leadList = trigger.new;
         for (Lead l: leadList) {
            l.firstname = 'Dr.' + l.firstname;
         }
      }
   ```
   > Calculate and set discount on insert/update on final price
   ```java
      trigger DiscountTrigger on Book__c(before insert, before update) {
         List < Book__c > books = Trigger.new;
         for (Book__c b: books) {
            //b.Price__c = b.Price__c * 0.9;
            b.Price__c *= 0.9;
         }
      }
   ```
3. 036 - test class for trigger
   > Getting started with trigger.
   ```java

   ```
## 4. Segregation of logic from trigger code
   > Use external class to write logic for trigger
   ```java
      // Trigger
      trigger HelloWorld on Account(before insert, before update) {
         List < Account > accs = Trigger.new;
         MyHelloWorld my = new MyHelloWorld(); // class instanciated to access function under it
         my.addHelloWorld(accs); // function invocked/called
      }
   ```
   ```java
      // Logic
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
## 5. Benifit of logic segregation
   > Contact object operations initited by different triggers
   ```java
      trigger AccountTrigger on Account(after insert) {
         if (trigger.isafter && trigger.isinsert) {
            ContactNameUpdate.newContactCreated(Trigger.new); // static method called without instanciation
         }
      }
   ```
   ```java
      trigger ContactTrigger on Contact(after insert) {
         if (trigger.isafter && trigger.isinsert) {
            ContactNameUpdate.updateCheckboxOnAccount(Trigger.new);// static method called without instanciation
         }
      }
   ```
   ```java
      public class ContactNameUpdate {
         public static void newContactCreated(List < Account > accList) {
            // logic will come here like bellow
            /*
            List < Contact > conList = new List < Contact > ();
            for (Account acc: accList) {
                  Contact con = new Contact(AccountId = acc.Id);
                  List < String > nameStr = acc.Name.split(' ');
                  if (nameStr.size() > 0)
                     con.LastName = nameStr[0];
                  if (nameStr.size() > 1)
                     con.FirstName = nameStr[1];
      
                  con.Email = 'info@websitedomain.tld';
                  con.Only_Default_Contact__c = TRUE;
                  conList.add(con);
            }
            insert conList;
            */
         }
         public static void updateCheckboxOnAccount(List < Contact > contactList) {
            // logic will come here like bellow
            /*
            Set < Id > accountIds = new Set < Id > ();
            for (Contact con: contactList) {
                  accountIds.add(con.AccountId);
            }
      
            List < Account > updatedAccounts = new List < Account > ();
            for (AggregateResult ar: [select count(id), AccountId from Contact where AccountId IN: accountIds group by AccountId having count(id) > 1]) {
                  updatedAccounts.add(new Account(Id = (Id) ar.get('AccountId'), Only_Default_Contact__c = false));
            }
      
            if (!updatedAccounts.isEmpty())
                  update updatedAccounts;
            */
         }
      }
   ```
## 6. Trigger for DML operations(for the case when trigger is written to serve multiple dml operation)
   > Identify the DML operation using nested condition
   ```java
      trigger Accountall on Account(before insert, before update, before delete, after insert, after update, after delete, after undelete)
      {
         //before
         if (trigger.IsBefore) {
            if (trigger.isinsert) {
                  // before insert code here    
            }
            if (trigger.isupdate) {
                  // before update code here
            }
            if (trigger.IsDelete) {
                  // before delete code here
            }
         }
         //after
         if (trigger.IsAfter) {
            if (trigger.isinsert) {
                  // after insert code here    
            }
            if (trigger.isupdate) {
                  // after update code here
            }
            if (trigger.IsDelete) {
                  // after delete code here
            }
            if (trigger.UnDelete) {
                  // after delete code here
            }
         } 
      }
   ```
   > Identify the DML operation using switch case
   ```java
      trigger Accountall on Account(before insert, before update, before delete, after insert, after update, after delete, after undelete)
      {
         switch on System.TriggerOperation {
            when 'BEFORE_INSERT' {
               // before insert code here
            }
            when 'BEFORE_UPDATE' {
               // before update code here
            }
            when 'BEFORE_DELETE' {
               // before delete code here
            }
            when 'AFTER_INSERT' {
               // after insert code here
            }
            when 'AFTER_UPDATE' {
               // after update code here
            }
            when 'AFTER_DELETE' {
               // after delete code here
            }
            when 'AFTER_UNDELETE' {
               // after undelete code here
            }
         }
      }
   ```
## 7. Building blocks for trigger
   > Get the list of changed(insert/ update/delete) records of connected sObject
   ```java
      // to get new version. 
      // available in : insert, update and undelete
      Trigger.new

      // to get old version
      // available in : update and delete
      Trigger.old
   ```
   > Get list of sObject using SOQL query
   ```java
      // Get all accounts which are connected to opportunity (account 1--* opportunity)
      List<Account> accounts = [SELECT Id FROM Account WHERE Id IN (SELECT AccountId FROM Opportunity)]
      // like above but only changed records (Trigger.old holds list of about to delete Account sObject records )
      List<Account> accounts = [SELECT Id FROM Account WHERE Id IN (SELECT AccountId FROM Opportunity) AND Id IN :Trigger.old]
      // get Account sObjects records which are about to delete
      List<Account> accounts = [SELECT Id, AccountName FROM Account WHERE Id IN :Trigger.old]
      // get Opportunity sObject records related to Account sObjects records which are about to delete
      List<Opportunity> opportunities = [SELECT Id, OpportunityName FROM Opportunity WHERE Account IN :Trigger.old]
   ```
   > Get the related opportunities (as Map) for the accounts in this trigger 
   ```java
      Map<Id,Account> acctsWithOpps = new Map<Id,Account>([SELECT Id,(SELECT Id FROM Opportunities) FROM Account WHERE Id IN :Trigger.New]);
   ```
   > Get list of account Ids (may contains duplicate) of changed Contacts sObject records
   ```java
      List<Id> lstAccIds = new List<Id>();
      for(Contact contact : Trigger.new) {
         if(contact.Accountid != null) {
            lstAccIds.add(con.AccountId);
         }
      }
   ```
   > Get list of unique account Ids of changed Contacts sObject records
   ```java
      Set<Id> setAccIds = new Set<Id>();
      for(Contact contact : Trigger.new) {
         if(contact.Accountid != null) {
            setAccIds.add(con.AccountId);
         }
      }
   ```
   > Check whether the prepared list/set has data or not
   ```java
      // test for data availability
      if(!setAccIds.isEmpty()){
         // data there
      }
      // or
      if(setAccIds.size() > 0){
         // data there
      }

      // test for data unavailability
      if(setAccIds.isEmpty()){
         // data not there
      }
      // or
      if(setAccIds.size() <= 0){
         // data not there
      }
   ``` 
   > Programic DMl operation
   ```java
      // create sObject(Account)
      Account newAccount = new Account();
      // create and fill data to new account
      Account newAccount = new Account();
      newAccount.phone='87482758745';
      newAccount.Rating='warm';
      newAccount.Industry='clould computing';
      // create, fill and insert new account
      Account newAccount = new Account();
      newAccount.phone='87482758745';
      newAccount.Rating='warm';
      newAccount.Industry='clould computing';
      Insert newAccount;

      // create multiple contacts and insert based on account records
         // here accountList holds a list of account records
         for (Account account: accountList){
            Contact newContact = new Contact();
            newContact.FirstName = account.FirstName;
            Insert newContact; // DML operation in for loop
         }
         // optimized version of previous example
         List<Contact> lstContact = new List<contact>();
         for (Account account: accountList){
            Contact newContact = new Contact();
            newContact.FirstName = account.FirstName;
            lstContact = newContact; // prepare list for single DML
         }
         if (lstContact.size() > 0) { // test of content before DML as DML with blank list throw error
            Insert lstContact;
         }
   ```
   ```java
      Account a = new Account(Name = 'Acme');
      insert a;  // Inserting the record automatically assigns a 
               // value to its ID field
      Contact c = new Contact(LastName = 'Weissman');
      c.AccountId = a.Id;
      // The new contact now points at the new account
      insert c;

      // A SOQL query accesses data for the inserted contact, 
      // including a populated c.account field
      c = [SELECT Account.Name FROM Contact WHERE Id = :c.Id];

      // Now fields in both records can be changed through the contact
      c.Account.Name = 'salesforce.com';
      c.LastName = 'Roth';

      // To update the database, the two types of records must be 
      // updated separately
      update c;         // This only changes the contact's last name
      update c.Account; // This updates the account name
   ```
   ```java
      // type 1:
      Account refAcct = new Account(externalId__c = '12345');
      Contact c = new Contact(Account = refAcct, LastName = 'Kay');
      insert c;

      // type 2:
      Account refAcct = [SELECT Id FROM Account WHERE externalId__c='12345'];
      Contact c = new Contact(Account = refAcct.Id);
      insert c;
   ```
   ```java
      for (Account a : [SELECT Id, Name, (SELECT LastName FROM Contacts) FROM Account WHERE Name = 'Acme']) {
         Contact[] cons = a.Contacts; // children are stored as plural name of the object
      }

      //The following example also works because we limit to only 1 contact
      for (Account a : [SELECT Id, Name, (SELECT LastName FROM Contacts LIMIT 1) FROM Account WHERE Name = 'testAgg']) {
         Contact c = a.Contacts;
      }
   ```
   > Using Agregarion
   ```java
      AggregateResult[] groupedResults = [SELECT CampaignId, AVG(Amount)aver FROM Opportunity GROUP BY CampaignId];
      for (AggregateResult ar : groupedResults)  {
         System.debug('Campaign ID' + ar.get('CampaignId'));
         System.debug('Average amount' + ar.get('aver'));
      }
   ```
   > Bulk update
   ```java
      // Use this format for efficiency if you are executing DML statements 
      // within the for loop
      for (List<Account> accts : [SELECT Id, Name FROM Account WHERE Name LIKE 'Acme%']) {
         // Your code here
         update accts;
      }
   ```
   > Variable in SOQL
   ```java
      Account A = new Account(Name='xxx');
      insert A;
      Account B;

      // A simple bind
      B = [SELECT Id FROM Account WHERE Id = :A.Id];

      // A bind with arithmetic
      B = [SELECT Id FROM Account 
         WHERE Name = :('x' + 'xx')];

      String s = 'XXX';

      // A bind with expressions
      B = [SELECT Id FROM Account 
         WHERE Name = :'XXXX'.substring(0,3)];

      // A bind with an expression that is itself a query result
      B = [SELECT Id FROM Account
         WHERE Name = :[SELECT Name FROM Account
                        WHERE Id = :A.Id].Name];

      Contact C = new Contact(LastName='xxx', AccountId=A.Id);
      insert new Contact[]{C, new Contact(LastName='yyy', 
                                          accountId=A.id)};

      // Binds in both the parent and aggregate queries
      B = [SELECT Id, (SELECT Id FROM Contacts
                     WHERE Id = :C.Id)
         FROM Account
         WHERE Id = :A.Id];

      // One contact returned
      Contact D = B.Contacts;

      // A limit bind
      Integer i = 1;
      B = [SELECT Id FROM Account LIMIT :i];

      // An OFFSET bind
      Integer offsetVal = 10;
      List<Account> offsetList = [SELECT Id FROM Account OFFSET :offsetVal];

      // An IN-bind with an Id list. Note that a list of sObjects
      // can also be used--the Ids of the objects are used for 
      // the bind
      Contact[] cc = [SELECT Id FROM Contact LIMIT 2];
      Task[] tt = [SELECT Id FROM Task WHERE WhoId IN :cc];

      // An IN-bind with a String list
      String[] ss = new String[]{'a', 'b'};
      Account[] aa = [SELECT Id FROM Account 
                     WHERE AccountNumber IN :ss];

      // A SOSL query with binds in all possible clauses

      String myString1 = 'aaa';
      String myString2 = 'bbb';
      Integer myInt3 = 11;
      String myString4 = 'ccc';
      Integer myInt5 = 22;

      List<List<SObject>> searchList = [FIND :myString1 IN ALL FIELDS 
                                       RETURNING 
                                          Account (Id, Name WHERE Name LIKE :myString2
                                                   LIMIT :myInt3), 
                                          Contact, 
                                          Opportunity, 
                                          Lead 
                                       WITH DIVISION =:myString4 
                                       LIMIT :myInt5];
   
   // distance function
   String units = 'mi';
   List<Account> accountList = 
      [SELECT ID, Name, BillingLatitude, BillingLongitude 
      FROM Account 
      WHERE DISTANCE(My_Location_Field__c, GEOLOCATION(10,10), :units) < 10]; 
   ```
   > loop SOQL
   ```java
      // wrong way
      for (Account acct : [SELECT Id, Name, (SELECT Id, Name FROM Contacts) 
                        FROM Account WHERE Id IN ('<ID value>')]) { 
         List<Contact> contactList = acct.Contacts; // Causes an error
         Integer count = acct.Contacts.size(); // Causes an error
      }

      // correct way
      for (Account acct : [SELECT Id, Name, (SELECT Id, Name FROM Contacts) 
                        FROM Account WHERE Id IN ('<ID value>')]) { 
         Integer count=0;
         for (Contact c : acct.Contacts) {
            count++;
         }
      }
   ```
   > Expanding sObject and List Expressions
   ```java
      Integer acctNameLength = new Account[]{new Account(Name='Acme')}[0].Name.length();
      String nameChange = [SELECT Name FROM Account][0].Name.toLowerCase();
   ```
   > Auto populate Map from SOQL
   ```java
      // Populate map from SOQL query
      Map<ID, Account> m = new Map<ID, Account>([SELECT Id, Name FROM Account LIMIT 10]);
      // After populating the map, iterate through the map entries
      for (ID idKey : m.keyset()) {
         Account a = m.get(idKey);
         System.debug(a);
      }
   ```
## 8. Trigger for validation
   > Account with account number present cannot be deleted
   ```java
      trigger Accountnumbervalidation on Account (before delete) {
         for(Account acc: trigger.old)
         {
            if(acc.accountnumber!=null)
            {
               acc.AccountNumber.addError('Account which having account number cannot be deleted');
            }
         }
      }
   ```
   > Prevent the deletion of accounts if they have related opportunities.
   ```java
      trigger AccountDeletion on Account (before delete) {
         for (Account a : [SELECT Id FROM Account
                           WHERE Id IN (SELECT AccountId FROM Opportunity) AND
                           Id IN :Trigger.old]) {
            Trigger.oldMap.get(a.Id).addError(
                  'Cannot delete account with related opportunities.');
         }
      }
   ``` 
## 9.  007 - Duplication check(!!!Need to work on map)
    > Check availablity of the same name of newly created account
   ```java
      trigger DuplicateAccount on Account(before insert) {
         // build teh set of names to make single query
         Set < String > accset = new Set < String > ();
         for (Account acc: Trigger.new) {
               accset.add(acc.name);
         }

         // build map to lookup quickly
         Map < String, id > mapacc = new Map < String, id > ();
         for (Account acc: [select id, name from Account where name IN accset]) { // single query made
               mapacc.put(acc.name, acc.id);
         }

         // check each insertable account name with teh map created
         for (Account acc: trigger.new) {
               id accountid = mapacc.get(acc.name);
               if (accountid != null) { // account name availability check
                  acc.name.addError('duplicate account');
               }
         }
      }
   ```
## 11. 002 - before save, fill some data
   > Before creating new Account record change the field values.
   ```java
      trigger Updateindusty on Account (before insert) {
         for(Account acc:trigger.new) {
            acc.phone='87482758745';
            acc.Rating='warm';
            acc.Industry='clould computing';
         }
      }
   ```
   > Before insert account or update owner of a account fill owner name as `sales_Rep__c` derived from owner
   ```java
      triggerUpdateSalesRep on Account(Before insert, Before Update) {
         Set <Id> setAccOwner = new Set<Id>();
         for(Account Acc: trigger.new) {
            setAccOwner.add(Acc.OwnerId);
         }
         Map<Id, User> User_map = new Map<Id, User>("select Name from User where id in: setAccOwner");
         for(Account Acc: Trigger.new) {
            User usr = User_map.get(Acc.OwnerId);
            Acc.sales_Rep__c = usr.Name;
         }
      }      
   ```
## 12. 026 - add child on parent add
   > On new account insert, create a default contact against each account
   ```java
      trigger NewContactCreateByAccount on Account (After insert) {
         List<Contact> contacts=new List<Contact>();
         For(Account acc:trigger.new){
            Contact cont=new Contact();
            cont.FirstName='Info';
            cont.LastName='Default';
            cont.Email='info@websitedomain.tld';
            contacts.add(cont);
         }
         if(contacts.size()>0){
            Insert contacts; // single DML for multiple records      
         }
      }
   ```
   > On new account insert, create a default contact against each account if createDefaultContact is checked in
   ```java
      trigger NewContactCreateByAccount on Account (After insert) {
         List<Contact> contacts=new List<Contact>();
         For(Account acc:trigger.new){
            if(acc.createDefaultContact__c){ 
               Contact cont=new Contact();
               cont.FirstName='Info';
               cont.LastName='Default';
               cont.Email='info@websitedomain.tld';
               contacts.add(cont);
            }
         }
         if(!contacts.isEmpty()){
            Insert contacts; // single DML for multiple records      
         }      
      }
   ```
## 15. 004 - on add/update create other object entry
    > Keep a copy of a account before update
   ```java
      trigger UpdateCopyAccount on Account (before update) {
         List<CopyAccount__c> lstCopyAccount__c = new List<CopyAccount__c>();
         for(Account acc : trigger.new) {
            System.debug('new value'+acc.name);
         }
         for(Account acc : trigger.old) {
            CopyAccount__c ca = new CopyAccount__c();
            ca.name=  acc.name;
            System.debug('old value'+acc.name);
            lstCopyAccount__c.add(ca);
         }
         if(!lstCopyAccount__c.isEmpty()){
            Insert lstCopyAccount__c;
         }
      }
   ```
16. 034 - like 004 with condition
    > Getting started with trigger.
   ```java

   ```
17. 008 - update all children by value from parent
    > Getting started with trigger.
   ```java

   ```
## 18. 010 - update all parent by the valueof child
   > Create the object called “Customer Project” and create Status field under this object with picklist data type (Values=Active, Inactive). Create the relationship between this custom object and Opportunity so that "Customer Project" is related list to the Opportunity.
   > Create "Active Customer project"‑ field on Opportunity object (Data Type=Checkbox)
   > The logic is when we create Customer Project for an Opportunity with the Status=Active, then "Active Customer project" check box on the Opportunity Detail page is automatically checked.
   ```java
      trigger UpdateCPActivenOnOppty on Customer_Project__c(after insert) {
         List < Opportunity > opps = new List < Opportunity > ();
         for (Customer_Project__c cp: Trigger.New) {
            if (cp.Status__c == 'Active') {
                  Opportunity opp = new Opportunity(id = cp.Opportunity__c);
                  opp.Active_Customer_Project__c = True;
                  //update opp;
                  opps.add(opp);
            }
         }
         update opps;
      }
   ```
   > Update phone number of account from Contact
   ```java
      trigger updatephone on Contact(after insert, after update) {
         set <id> ids = new set <id> ();
         List <account> acclist = new List <account> ();
         for (contact con: trigger.new) {
            ids.add(con.AccountId);
         }

         Map <Id, Account> accountMap = new Map <Id, Account>([Select Id, Phone From Account Where Id In: ids]);
         for (contact c: trigger.new) {
            Account acc = accountMap.get(c.AccountId);
            if (acc != null) {
               acc.Phone = c.Phone;
               acclist.add(acc);
            }
         }
         update acclist;
      }
   ```
## 19. 009 - rollup summery
   > On Contact record create, update account field which holds teh underline contacts count
   ```java
      trigger NoOfcontacts on Contact(after insert) {
         Set<id> relAccIds = new Set<id>();
         for (Contact contact: trigger.new) {
            relAccIds.add(contact.AccountId);
         }
         Map<Id,Account> acctsWithOpps = new Map<Id,Account>([SELECT Id, Name,(SELECT Id, Name FROM Contacts) FROM Account WHERE ID IN relAccIds]);
         List<Account> accounts =  New List<Account>();
         for(Id key: acctsWithOpps.keyset()){
            Account acc = acctsWithOpps.get(key)
            acc.No_of_contacts__c = acc.Contacts.size();
            accounts.add(acc);
         }
         if(accounts.size()>0){
            Update accounts;
         }
      }
   ```
   ```java
      // Lengthy way
      trigger Noofcontacts on Contact(after insert) {
         Set<id> ids = new Set<id>();
         List<Account> acclist = new List<Account>();
         List<Account> listofacc = new List<Account>();
         List<Contact> conlist = new List<Contact>();
         List<COntact> listofcon = new List<Contact>();
         Map<id, integer> mapcount = new Map<id, integer>();
         for (contact con: trigger.new) {
            ids.add(con.accountid);
         }
      
         acclist = [select id, name from account where id in: ids];
         conlist = [select id, name, accountid from contact where accountid in: ids];
         for(account acc: acclist) {
            for(contact con: conlist) {
               if(con.accountid == acc.id) {
                  listofcon.add(con);
                  mapcount.put(con.AccountId, listofcon.size());
               }
            }
         }
         if(acclist.size() > 0) {
            for(Account acc: acclist){
               if(mapcount.get(acc.id) == null) {
                  acc.No_of_contacts__c = 0;
               }else {
                  acc.No_of_contacts__c = mapcount.get(acc.id);
                  listofacc.add(acc);
               }
            }
         }
         if(listofacc.size() > 0){
            update listofacc;
         }
      }
   ```
20. 011 - update other filed on change of one from other object
    > Getting started with trigger.
   ```java

   ```
21. Restrict delete opereation in presense of related child data
   
22. 014 - delete children on parent delete
    > Getting started with trigger.
   ```java

   ```
22. 019 - like 011 but delete operation
    > Getting started with trigger.
   ```java

   ```
23. 006 - Mark row for undelete
    > Getting started with trigger.
   ```java

   ```
24. 015 - undelete children on parent undelete
    > Getting started with trigger.
   ```java

   ```
## 25. Examples
   > Create the object called "Customer" and create the Master-Detail Relationship on Customer object so that Customer will be the related list to Account record. Create the field called "Account Manager" on Customer object which is lookup to the user object.
   > Now Logic is when we create Customer record for account record, then the user in Account Manager field will be automatically added to Account Team of that associated account.
   ```java
      trigger InsertAccountTeam on Customer__c(after insert) {
         List < AccountTeamMember > atm_list = new List < AccountTeamMember > ();
         AccountTeamMember atm = new AccountTeamMember();
         List < AccountShare > newShare = new List < AccountShare > ();
         if (trigger.isInsert)
         {
            For(Customer__c c: Trigger.new)
            {
               if (c.Account_Manager__c != null) {
                  // create Account member with pre fill data
                  atm = new AccountTeamMember(accountid=c.Account__c, teamMemberRole='Account Manager', UserId = c.Account_Manager__c);
                  atm_list.add(atm);
                  
                  // create AccountShare blank and then fill data
                  AccountShare shares = new AccountShare();
                  shares.AccountId = c.Account__c;
                  shares.UserOrGroupId = c.Account_Manager__c;
                  shares.AccountAccessLevel = 'Read/Write';
                  shares.OpportunityAccessLevel = 'Read Only';
                  shares.CaseAccessLevel = 'Read Only';
                  newShare.add(shares);
               }
            }
            if (atm_list != null)
               Insert atm_list; // Insert using insert method
            if (newShare != null && newShare.size() > 0)
               List < Database.saveresult > sr = Database.insert(newShare, false); // Insert using Database method and return
         }
      }

      // practice:
      // 1. When we create the Opportunity with the Probability=20, then the opportunity owner will be automatically added to Account Team of the associated account for that Opportunity.
   ```



027 - update by prefix on each entry
036 - test class for trigger
035 - logic in external class
033 - common object update by more trigger
005 - All DML operations
003 - Validation
030 - delete confirmation
001 - Duplication check
007 - optimized duplicate check
002 - before save, fill some data
026 - add child on parent add
017 - like 011 but return of DML operation
021 - like 017 with aditional condition
004 - on add/update create other object entry
034 - like 004 with condition
008 - update all children by value from parent
010 - update all parent by the valueof child
009 - rollup summery
011 - update other filed on change of one from other object
014 - delete children on parent delete
019 - like 011 but delete operation
006 - Mark row for undelete
015 - undelete children on parent undelete

https://trailhead.salesforce.com/en/content/learn/modules/apex_triggers/apex_triggers_intro
https://developer.salesforce.com/docs/atlas.en-us.224.0.apexcode.meta/apexcode/apex_triggers.htm
