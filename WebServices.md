# [Apex Web Services](https://trailhead.salesforce.com/modules/apex_integration_services/units/apex_integration_webservices)

## Create an Apex REST service that returns an account and it's contacts.

To pass this challenge, create an Apex REST class that is accessible at '/Accounts/<Account_ID>/contacts'. The service will return the account's ID and Name plus the ID and Name of all contacts associated with the account. Write unit tests that achieve 100% code coverage for the class and run your Apex tests.

* The Apex class must be called 'AccountManager'.
* The Apex class must have a method called 'getAccount' that is annotated with @HttpGet and returns an Account object.
* The method must return the ID and Name for the requested record and all associated contacts with their ID and Name.
* The unit tests must be in a separate Apex class called 'AccountManagerTest'.
* The unit tests must cover all lines of code included in the AccountManager class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```

@RestResource(urlMapping='/Accounts/*/contacts')
global with sharing class AccountManager{
    @HttpGet
    global static Account getAccount(){
        RestRequest req = RestContext.request;
        String accId = req.requestURI.substringBetween('Accounts/', '/contacts');
        Account acc = [SELECT Id, Name, (SELECT Id, Name FROM Contacts) 
                       FROM Account WHERE Id = :accId];
        
        return acc;
    }
}

```

### Apex Test Class

```

@IsTest
private class AccountManagerTest{
    @isTest static void testAccountManager(){
        Id recordId = getTestAccountId();
        // Set up a test request
        RestRequest request = new RestRequest();
        request.requestUri =
            'https://ap5.salesforce.com/services/apexrest/Accounts/'+ recordId +'/contacts';
        request.httpMethod = 'GET';
        RestContext.request = request;
        
        // Call the method to test
        Account  acc = AccountManager.getAccount();
        
        // Verify results
        System.assert(acc != null);
    }
    
    private static Id getTestAccountId(){
        Account acc = new Account(Name = 'TestAcc2');
        Insert acc;
         
        Contact con = new Contact(LastName = 'TestCont2', AccountId = acc.Id);
        Insert con;
        
        return acc.Id;
    }
}

``` 

> Source: http://sfdccodepractices.blogspot.com/2017/07/create-apex-rest-service-that-returns.html
