# [Use Future Methods](https://trailhead.salesforce.com/modules/asynchronous_apex/units/async_apex_future_methods)

## Create an Apex class that uses the @future annotation to update Account records.

Create an Apex class with a method using the @future annotation that accepts a List of Account IDs and updates a custom field on the Account object with the number of contacts associated to the Account. Write unit tests that achieve 100% code coverage for the class.

* Create a field on the Account object called 'Number_of_Contacts__c' of type Number. This field will hold the total number of Contacts for the Account.
* Create an Apex class called 'AccountProcessor' that contains a 'countContacts' method that accepts a List of Account IDs. This method must use the @future annotation.
* For each Account ID passed to the method, count the number of Contact records associated to it and update the 'Number_of_Contacts__c' field with this value.
* Create an Apex test class called 'AccountProcessorTest'.
* The unit tests must cover all lines of code included in the AccountProcessor class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```

public class AccountProcessor 
{
  @future
  public static void countContacts(Set<id> setId) 
  {
      List<Account> lstAccount = [select id,Number_of_Contacts__c , (select id from contacts ) from account where id in :setId ];
      for( Account acc : lstAccount )
      {
          List<Contact> lstCont = acc.contacts ;
          
          acc.Number_of_Contacts__c = lstCont.size();
      }
      update lstAccount;
  }
}

```

### Apex Test Class

```

@IsTest
public class AccountProcessorTest {
    public static testmethod void TestAccountProcessorTest() 
    {
        Account a = new Account();
        a.Name = 'Test Account';
        Insert a;

        Contact cont = New Contact();
        cont.FirstName ='Bob';
        cont.LastName ='Masters';
        cont.AccountId = a.Id;
        Insert cont;
        
        set<Id> setAccId = new Set<ID>();
        setAccId.add(a.id);
 
        Test.startTest();
            AccountProcessor.countContacts(setAccId);
        Test.stopTest();
        
        Account ACC = [select Number_of_Contacts__c from Account where id = :a.id LIMIT 1];
        System.assertEquals ( Integer.valueOf(ACC.Number_of_Contacts__c) ,1);
  }
  
}

``` 

> Source: https://developer.salesforce.com/forums/?id=906F0000000D8hwIAC
