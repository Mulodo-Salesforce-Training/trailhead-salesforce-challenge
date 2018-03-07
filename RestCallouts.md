# [Apex REST Callouts](https://trailhead.salesforce.com/modules/apex_integration_services/units/apex_integration_rest_callouts)

## Create an Apex class that calls a REST endpoint and write a test class.

To pass this challenge, create an Apex class that calls a REST endpoint to return the name of an animal, write unit tests that achieve 100% code coverage for the class using a mock response, and run your Apex tests.

* The Apex class must be called 'AnimalLocator', have a 'getAnimalNameById' method that accepts an Integer and returns a String.
* The 'getAnimalNameById' method must call https://th-apex-http-callout.herokuapp.com/animals/:id, using the ID passed into the method. The method returns the value of the 'name' property (i.e., the animal name).
* Create a test class named AnimalLocatorTest that uses a mock class called AnimalLocatorMock to mock the callout response.
* The unit tests must cover all lines of code included in the AnimalLocator class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```

public class AnimalLocator
{

  public static String getAnimalNameById(Integer id)
   {
        Http http = new Http();
        HttpRequest request = new HttpRequest();
        request.setEndpoint('https://th-apex-http-callout.herokuapp.com/animals/'+id);
        request.setMethod('GET');
        HttpResponse response = http.send(request);
          String strResp = '';
           system.debug('******response '+response.getStatusCode());
           system.debug('******response '+response.getBody());
        // If the request is successful, parse the JSON response.
        if (response.getStatusCode() == 200) 
        {
            // Deserializes the JSON string into collections of primitive data types.
           Map<String, Object> results = (Map<String, Object>) JSON.deserializeUntyped(response.getBody());
            // Cast the values in the 'animals' key as a list
           Map<string,object> animals = (map<string,object>) results.get('animal');
            System.debug('Received the following animals:' + animals );
            strResp = string.valueof(animals.get('name'));
            System.debug('strResp >>>>>>' + strResp );
        }
        return strResp ;
   }
  
}

```

### Apex Test Class

```

@isTest
private class AnimalLocatorTest{
    @isTest static  void AnimalLocatorMock1() {
        Test.SetMock(HttpCallOutMock.class, new AnimalLocatorMock());
        string result=AnimalLocator.getAnimalNameById(3);
        string expectedResult='chicken';
        System.assertEquals(result, expectedResult);
    }
}

``` 

### Apex Mock Test Class

```

@isTest
global class AnimalLocatorMock implements HttpCalloutMock {
    global HTTPResponse respond(HTTPRequest request) {
         HttpResponse response = new HttpResponse();
        response.setHeader('Content-Type', 'application/json');
        response.setBody('{"animal":{"id":1,"name":"chicken","eats":"chicken food","says":"cluck cluck"}}');
        response.setStatusCode(200);
        return response;
    }
}

```

### Setup Remote site settings

![Image of Remote site settings](https://github.com/Mulodo-Salesforce-Training/trailhead-salesforce-challenge/blob/master/img/RestCallouts.png)

> Source: https://developer.salesforce.com/forums/?id=906F0000000D6tHIAS
