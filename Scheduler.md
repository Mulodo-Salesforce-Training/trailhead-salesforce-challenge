# [Schedule Jobs Using the Apex Scheduler](https://trailhead.salesforce.com/modules/asynchronous_apex/units/async_apex_scheduled)

## Create an Apex class that uses Scheduled Apex to update Lead records.

Create an Apex class that implements the Schedulable interface to update Lead records with a specific LeadSource. Write unit tests that achieve 100% code coverage for the class. This is very similar to what you did for Batch Apex.

* Create an Apex class called 'DailyLeadProcessor' that uses the Schedulable interface.
* The execute method must find the first 200 Leads with a blank LeadSource field and update them with the LeadSource value of 'Dreamforce'.
* Create an Apex test class called 'DailyLeadProcessorTest'.
* In the test class, insert 200 Lead records, schedule the DailyLeadProcessor class to run and test that all Lead records were updated correctly.
* The unit tests must cover all lines of code included in the DailyLeadProcessor class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```

global class DailyLeadProcessor implements Schedulable{
    global void execute(SchedulableContext ctx){
        List<Lead> leads = [SELECT Id, LeadSource FROM Lead WHERE LeadSource = ''];
        
        if(leads.size() > 0){
            List<Lead> newLeads = new List<Lead>();
            
            for(Lead lead : leads){
                lead.LeadSource = 'DreamForce';
                newLeads.add(lead);
            }
            
            update newLeads;
        }
    }
}

```

### Apex Test Class

```

@isTest
private class DailyLeadProcessorTest{
    //Seconds Minutes Hours Day_of_month Month Day_of_week optional_year
    public static String CRON_EXP = '0 0 0 2 6 ? 2022';
    
    static testmethod void testScheduledJob(){
        List<Lead> leads = new List<Lead>();
        
        for(Integer i = 0; i < 200; i++){
            Lead lead = new Lead(LastName = 'Test ' + i, LeadSource = '', Company = 'Test Company ' + i, Status = 'Open - Not Contacted');
            leads.add(lead);
        }
        
        insert leads;
        
        Test.startTest();
        // Schedule the test job
        String jobId = System.schedule('Update LeadSource to DreamForce', CRON_EXP, new DailyLeadProcessor());
        
        // Stopping the test will run the job synchronously
        Test.stopTest();
    }
}

```

> Source: https://developer.salesforce.com/forums/?id=906F0000000DAGEIA4
