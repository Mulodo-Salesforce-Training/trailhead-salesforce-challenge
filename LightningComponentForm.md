# [Lightning Components Basics :: Input Data Using Forms](https://trailhead.salesforce.com/modules/lex_dev_lc_basics/units/lex_dev_lc_basics_forms)

## Create a Form to Enter New Items

In this challenge you'll create a form to enter new items, a list to display the items entered, and add SLDS styling. First, to make our camping list look more appealing, change the campingHeader component to use lightning:layout and SLDS. Similar to the unit, style the Camping List H1 inside the slds-page-header. Add the action:goal SLDS icon using lightning:icon.

Next, modify the campingList component to contain a new item input form and an iteration of campingListItem components for displaying the items entered. Here are additional details for the modifications to the campingList component.

* Add an attribute named items with the type of an array of camping item custom objects.
* Add an attribute named newItem of type Camping_Item__c with default quantity and price values of 0.
* The component displays the Name, Quantity, Price, and Packed form fields with the appropriate input component types and values from the newItem attribute. The Quantity field accepts a number that's at least 1.
* Submitting the form executes the action clickCreateItem in the JavaScript controller.
* If the form is valid, the JavaScript controller pushes the newItem onto the array of existing items, triggers the notification that the items value provider has changed, and resets the newItem value provider with a blank sObjectType of Camping_Item__c. For this challenge, place the code in your component's controller, not the helper. 

### Lightning Component: campingHeader.cmp

```
<aura:component >
    <lightning:layout class="slds-page-header slds-page-header--object-home">
    <lightning:layoutItem >
    <lightning:icon iconName="action:goal" alternativeText="My Camping"/>
    </lightning:layoutItem>
        <lightning:layoutItem padding="horizontal-small">
        <div class="page-section page-header">
          <h1 class="slds-text-heading--label">Camping</h1>
          <h2 class="slds-text-heading--medium">My Camping</h2>
        </div>
    </lightning:layoutItem>
    </lightning:layout>
</aura:component>

```

### Lightning Component: campingList.cmp

```
<aura:component >
    <aura:attribute name="items" type="Camping_Item__c[]"/>
        <aura:attribute name="newItem" type="Camping_Item__c" default="{'Name':'',
            'Quantity__c':0,
            'Price__c':0,
            'Packed__c':false,
            'sobjectType':'Camping_Item__c'}"/>
            
            <!-- NEW Campaing FORM -->
            <div class="slds-col slds-col--padded slds-p-top--large">
                
                
                <c:campingHeader/>
                <div aria-labelledby="newCampaingForm">
                    
                    <!-- BOXED AREA -->
                    <fieldset class="slds-box slds-theme--default slds-container--small">

                        <legend id="newCampaingForm" class="slds-text-heading--smallslds-p-vertical--medium">
                            Add Expense
                        </legend>

                        <!-- CREATE NEW Campaing FORM -->
                        <form class="slds-form--stacked">

                        <!-- For Name Field -->
                        <lightning:input aura:id="expenseform" label="Camping Name"
                            name="expensename"
                            value="{!v.newItem.Name}"
                            required="true"/>
                        <!-- For Quantity Field -->
                        <lightning:input type="number" aura:id="campingform" label="Quantity"
                            name="expenseamount"
                            min="1"
                            value="{!v.newItem.Quantity__c}"
                            messageWhenRangeUnderflow="Enter minimum 1 Quantity"/>
                        <!-- For Price Field -->
                        <lightning:input aura:id="campingform" label="Price"
                            formatter="currency"
                            name="expenseclient"
                            value="{!v.newItem.Price__c}"/>
                        <!-- For Check Box -->
                        <lightning:input type="checkbox" aura:id="campingform" label="Packed"
                            name="expreimbursed"
                            checked="{!v.newItem.Packed__c}"/>

                        <lightning:button label="Create Camping"
                            class="slds-m-top--medium"
                            variant="brand"
                            onclick="{!c.clickCreateItem}"/>
                        </form>
                        <!-- / CREATE NEW EXPENSE FORM -->
                    </fieldset>
                    <!-- / BOXED AREA -->
                                        
                </div>
            <!-- / CREATE NEW EXPENSE -->
            </div>
            <!-- ITERATIING ITEM LISTS -->
            <div class="slds-card slds-p-top--medium">
                
                <c:campingHeader/>
                
                <section class="slds-card__body">
                    <div id="list" class="row">
                        <aura:iteration items="{!v.items}" var="item">
                            <c:campingListItem item="{!item}"/>
                        </aura:iteration>
                    </div>
                </section>
            </div>
            <!-- / ITERATIING ITEM LISTS -->
</aura:component>

```

### Controller: campingListController.js

```
({
clickCreateItem : function(component, event, helper) {
    var validCamping = component.find('campingform').reduce(function (validSoFar, inputCmp) {
        // Displays error messages for invalid fields
        inputCmp.showHelpMessageIfInvalid();
        return validSoFar && inputCmp.get('v.validity').valid;
    }, true);
     
    if(validCamping){
        var newCampingItem = component.get("v.newItem");
        //helper.createCamping(component,newCampingItem);
        var campings = component.get("v.items");
        var item = JSON.parse(JSON.stringify(newCampingItem));
         
        campings.push(item);
         
        component.set("v.items",campings);
        component.set("v.newItem",{ 'sobjectType': 'Camping_Item__c','Name': '','Quantity__c': 0,
        'Price__c': 0,'Packed__c': false });
    }
}
})

```

### Lightning Component: campingListItem.cmp

```

<aura:component  implements="force:appHostable">

    <aura:attribute name="item" type="Camping_Item__c"/>
    
    <p>Name:
        <ui:outputText value="{!v.item.Name}"/>
    </p>
    <p>Price:
        <ui:outputCurrency value="{!v.item.Price__c}"/>
    </p>
    <p>Quantity:
        <ui:outputNumber value="{!v.item.Quantity__c}"/>
    </p>
    <p>Packed:
        <ui:outputCheckbox value="{!v.item.Packed__c}"/>
    </p>
</aura:component>

```

### Lightning Application: campingApp.app

```

<aura:application extends="force:slds">
    <c:campingList/>
</aura:application>

```

### Image

![Image](https://github.com/Mulodo-Salesforce-Training/trailhead-salesforce-challenge/blob/master/img/Form.png)

> Source: https://developer.salesforce.com/forums/?id=9060G000000MPn2QAG
