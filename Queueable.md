# [Control Processes with Queueable Apex](https://trailhead.salesforce.com/modules/asynchronous_apex/units/async_apex_queueable)

## Create an Queueable Apex class that inserts Contacts for Accounts.

Create a Queueable Apex class that inserts the same Contact for each Account for a specific state. Write unit tests that achieve 100% code coverage for the class.

* Create an Apex class called 'AddPrimaryContact' that implements the Queueable interface.
* Create a constructor for the class that accepts as its first argument a Contact sObject and a second argument as a string for the State abbreviation.
* The execute method must query for a maximum of 200 Accounts with the BillingState specified by the State abbreviation passed into the constructor and insert the Contact sObject record associated to each Account. Look at the sObject clone() method.
* Create an Apex test class called 'AddPrimaryContactTest'.
* In the test class, insert 50 Account records for BillingState "NY" and 50 Account records for BillingState "CA". Create an instance of the AddPrimaryContact class, enqueue the job and assert that a Contact record was inserted for each of the 50 Accounts with the BillingState of "CA".
* The unit tests must cover all lines of code included in the AddPrimaryContact class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```

public class AddPrimaryContact implements Queueable{
    Contact con;
    String state;
    
    public AddPrimaryContact(Contact con, String state){
        this.con = con;
        this.state = state;
    }
    public void execute(QueueableContext qc){
        List<Account> lstOfAccs = [SELECT Id FROM Account WHERE BillingState = :state LIMIT 200];
        
        List<Contact> lstOfConts = new List<Contact>();
        for(Account acc : lstOfAccs){
            Contact conInst = con.clone(false,false,false,false);
            conInst.AccountId = acc.Id;
        
            lstOfConts.add(conInst);
        }
        
        INSERT lstOfConts;
    }
}

```

### Apex Test Class

```

@isTest
public class AddPrimaryContactTest{
    @testSetup
    static void setup(){
        List<Account> lstOfAcc = new List<Account>();
        for(Integer i = 1; i <= 100; i++){
            if(i <= 50)
                lstOfAcc.add(new Account(name='AC'+i, BillingState = 'NY'));
            else
                lstOfAcc.add(new Account(name='AC'+i, BillingState = 'CA'));
        }
        
        INSERT lstOfAcc;
    }

    static testmethod void testAddPrimaryContact(){
        Contact con = new Contact(LastName = 'TestCont');
        AddPrimaryContact addPCIns = new AddPrimaryContact(CON ,'CA');
        
        Test.startTest();
        System.enqueueJob(addPCIns);
        Test.stopTest();
        
        System.assertEquals(50, [select count() from Contact]);
    }
}

``` 

> Source: http://sfdccodepractices.blogspot.com/2017/07/create-queueable-apex-class-that.html
