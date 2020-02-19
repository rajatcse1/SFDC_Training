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
      List<Account> accounts = [SELECT Id, OpportunityName FROM Opportunity WHERE Account IN :Trigger.old]
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
9.  001 - Duplication check
    > Getting started with trigger.
   ```java

   ```
11. 007 - optimized duplicate check
    > Getting started with trigger.
   ```java

   ```
11. 002 - before save, fill some data
    > Getting started with trigger.
   ```java

   ```
12. 026 - add child on parent add
    > Getting started with trigger.
   ```java

   ```
13. 017 - like 011 but return of DML operation
    > Getting started with trigger.
   ```java

   ```
14. 021 - like 017 with aditional condition
    > Getting started with trigger.
   ```java

   ```
15. 004 - on add/update create other object entry
    > Getting started with trigger.
   ```java

   ```
16. 034 - like 004 with condition
    > Getting started with trigger.
   ```java

   ```
17. 008 - update all children by value from parent
    > Getting started with trigger.
   ```java

   ```
18. 010 - update all parent by the valueof child
    > Getting started with trigger.
   ```java

   ```
19. 009 - rollup summery
    > Getting started with trigger.
   ```java

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
25. 


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
