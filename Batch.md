# [Use Batch Apex](https://trailhead.salesforce.com/modules/asynchronous_apex/units/async_apex_batch)

## Create an Apex class that uses Batch Apex to update Lead records.

Create an Apex class that implements the Database.Batchable interface to update all Lead records in the org with a specific LeadSource. Write unit tests that achieve 100% code coverage for the class.

* Create an Apex class called 'LeadProcessor' that uses the Database.Batchable interface.
* Use a QueryLocator in the start method to collect all Lead records in the org.
* The execute method must update all Lead records in the org with the LeadSource value of 'Dreamforce'.
* Create an Apex test class called 'LeadProcessorTest'.
* In the test class, insert 200 Lead records, execute the 'LeadProcessor' Batch class and test that all Lead records were updated correctly.
* The unit tests must cover all lines of code included in the LeadProcessor class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```

global class LeadProcessor implements 
Database.Batchable<sObject>, Database.Stateful {
    
    // instance member to retain state across transactions
    global Integer recordsProcessed = 0;

    global Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator('SELECT Id, LeadSource FROM Lead');
    }

    global void execute(Database.BatchableContext bc, List<Lead> scope){
        // process each batch of records
        List<Lead> leads = new List<Lead>();
        for (Lead lead : scope) {
            
                lead.LeadSource = 'Dreamforce';
                // increment the instance member counter
                recordsProcessed = recordsProcessed + 1;
   
        }
        update leads;
    }    

    global void finish(Database.BatchableContext bc){
        System.debug(recordsProcessed + ' records processed. Shazam!');
        
    }    
}

```

### Apex Test Class

```

@isTest
public class LeadProcessorTest {
 @testSetup 
    static void setup() {
        List<Lead> leads = new List<Lead>();
        // insert 200 leads
        for (Integer i=0;i<200;i++) {
            leads.add(new Lead(LastName='Lead '+i, 
                Company='Lead', Status='Open - Not Contacted'));
        }
        insert leads;
    }

    static testmethod void test() {  
        Test.startTest();
        LeadProcessor lp = new LeadProcessor();
        Id batchId = Database.executeBatch(lp, 200);
        Test.stopTest();

        // after the testing stops, assert records were updated properly
        System.assertEquals(200, [select count() from lead where LeadSource = 'Dreamforce']);
    }
}

``` 

> Source: https://developer.salesforce.com/forums/?id=9060G000000BfZYQA0
