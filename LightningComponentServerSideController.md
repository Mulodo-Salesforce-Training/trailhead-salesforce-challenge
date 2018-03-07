# [Lightning Components Basics :: Connect to Salesforce with Server-Side Controllers](https://trailhead.salesforce.com/modules/lex_dev_lc_basics/units/lex_dev_lc_basics_server)

## Save and Load Records with a Server-Side Controller

Persist your records to the database using a server-side controller. The campingList component loads existing records when it starts up and saves records to the database when the form is submitted.

* Create a CampingListController Apex class with a getItems method and saveItem method.
* Add a doInit initialization handler that loads existing records from the database when the component starts up.
* Modify the JavaScript controller to use a createItem method in the helper to save records to the database from a valid form submission. The new items are added to the controller's items value provider.

### Apex Class

```
public class CampingListController {
    @auraenabled
    public static List<Camping_Item__c> getItems () {
        List<Camping_Item__c> CI = [select id, name,price__c,Quantity__c,Packed__c from Camping_Item__c ];
        return CI;
    }
    @auraenabled
    public static Camping_Item__c saveItem (Camping_Item__c item){
        insert item;
        return item;
    }
}

```

### Lightning Component: campingList.cmp

```
<aura:component controller="CampingListController">
    <aura:handler name="init" value="{!this}" action="{!c.doInit}"/>
	<aura:attribute name="items" type="Camping_Item__c[]"/>
    <aura:attribute name="er" type="boolean" default="false"/>
    <aura:attribute name="newItem" type="Camping_Item__c"    default="{ 'sobjectType': 'Camping_Item__c',
                         'Name': '',
                         'Price__c': 0,
                         'Quantity__c': 0,                         
                         'Packed__c': false
                       }"/>
    <ui:inputText value="{!v.newItem.Name}" aura:id="name" label="name"/>
    <ui:inputCheckbox value="{!v.newItem.Packed__c}" aura:id="Packed" label="Packed"/>
    <ui:inputCurrency value="{!v.newItem.Price__c}"  aura:id="Price" label="Price"/>
    <ui:inputNumber value="{!v.newItem.Quantity__c}" aura:id="Quantity" label="Quantity"/>
    <ui:button label="Create Camping" press="{!c.createItem}" aura:id="button"/>
    <br/>
	<aura:iteration items="{!v.items}" var="PerItem">
        
        <c:campingListItem item="{!PerItem}" />
    </aura:iteration>
</aura:component>

```

### Controller: campingListController.js

```
({
	
    doInit  : function(component, event, helper) {
		var action = component.get("c.getItems");
        action.setCallback(this, function(response){
            var state = response.getState();
           
            if (component.isValid() && state === "SUCCESS") {
           
               
                component.set("v.items", response.getReturnValue());
                 
            }
        });
        
        $A.enqueueAction(action);
	},
    
    createItem : function(component, event, helper){
        
        helper.validateFields (component,component.find("name"));
        helper.validateFields (component,component.find("Price"));
        helper.validateFields (component,component.find("Quantity"));
        if(component.get("v.er") === false)
        {     
            var Item = component.get("v.newItem");            
            helper.createItem (component,Item);             
                       
        }
	}    
})

```

### Helper Controller: campingListHelper.js

```
({
	
    validateFields : function (component,field) {
        
        var nameField = field;
        console.log('yes:'+nameField);
        var expname = nameField.get("v.value"); 
        if ($A.util.isEmpty(expname)){
           component.set("v.er",true);
           nameField.set("v.errors", [{message:"this field can't be blank."}]);
        }
        else {
            nameField.set("v.errors", null);
        }
    },
    
    createItem : function (component,Item){         
        var action = component.get("c.saveItem");
        action.setParams({"item":Item});
        action.setCallback(this,function(response){
            var state = response.getState();
            if (component.isValid() && state === "SUCCESS") {
                var campings = component.get("v.items");
                campings.push(response.getReturnValue());
                component.set("v.items", campings);
            }
        });
       $A.enqueueAction(action);        
    }
})

```

### Lightning Application: campingApp.app

```

<aura:application extends="force:slds">
    <c:campingList/>
</aura:application>

```

> Source: https://developer.salesforce.com/forums/?id=9060G000000I6ZaQAK
