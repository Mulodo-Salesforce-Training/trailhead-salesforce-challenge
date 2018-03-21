# [Lightning Components Basics :: Connect Components with Events](https://trailhead.salesforce.com/modules/lex_dev_lc_basics/units/lex_dev_lc_basics_events)

## Refactor Components and Communicate with Events

Refactor the input form for camping list items into its own component and communicate with component events.

* Replace the HTML form in the campingList component with a new campingListForm component that calls the clickCreateItem JavaScript controller action when clicked.
* The campingList component listens for a c:addItemEvent event and executes the action handleAddItem in the JavaScript controller. The handleAdditem method saves the record to the database and adds the record to the items value provider.
* The addItemEvent event is of type component and has a Camping_Item__c type attribute named item.
* The campingListForm registers an addItem event of type c:addItemEvent.
* The campingListFormController JavaScript controller calls the helper's createItem method if the form is valid.
* The campingListFormHelper JavaScript helper creates an addItem event with the item to be added and then fires the event. It then resets the newItem value provider with a blank sObjectType of type Camping_Item__c.

### Lightning Component: campingList Component

```
<aura:component controller="CampingListController">
	
    <aura:handler name="init" action="{!c.doInit}" value="{!this}"/>
    
    <aura:handler name="addItem" event="c:addItemEvent"
   	action="{!c.handleAddItem }"/>
    

    
    <aura:attribute name="items" type="Camping_Item__c[]"/>
    
    <ol>
    <li>Bug Spray</li>
    <li>Bear Repellant</li>
    <li>Goat Food</li>
    </ol>
    
       <!-- NEW ITEM FORM -->
    <div class="slds-col slds-col--padded slds-p-top--large">

        <c:campingListForm />

    </div>
    <!-- / NEW ITEM FORM -->    
   

    <div class="slds-card slds-p-top--medium">
        <header class="slds-card__header">
            <h3 class="slds-text-heading--small">Items</h3>
        </header>
        
        <section class="slds-card__body">
            <div id="list" class="row">
                <aura:iteration items="{!v.items}" var="items">
                    <c:campingListItem item="{!item}"/>
                </aura:iteration>
            </div>
        </section>
    </div>

</aura:component>

```

### campingListController

```
({
    // Load items from Salesforce
	doInit: function(component, event, helper) {

    // Create the action
    var action = component.get("c.getItems");

    // Add callback behavior for when response is received
    action.setCallback(this, function(response) {
        var state = response.getState();
        if (component.isValid() && state === "SUCCESS") {
            component.set("v.items", response.getReturnValue());
        }
        else {
            console.log("Failed with state: " + state);
        }
    });

    // Send action off to be executed
    $A.enqueueAction(action);
},

    
    handleAddItem: function(component, event, helper) {
    //   var newItem = event.getParam("item");
    //helper.addItem(component, newItem);
    var action = component.get("c.saveItem");
    		action.setParams({"item": newItem});
    		action.setCallback(this, function(response){
        		var state = response.getState();
        		if (component.isValid() && state === "SUCCESS") {
            		// all good, nothing to do.
            var items = component.get("v.items");
            items.push(response.getReturnValue());
            component.set("v.items", items);
        		}
    		});
    		$A.enqueueAction(action);
        		}
    
              
})

```

### campingList Helper

```
({
   addItem: function(component, item) {
    this.saveItem(component, item, function(response){
        var state = response.getState();
        if (component.isValid() && state === "SUCCESS") {
            // all good, nothing to do.
         /*   var items = component.get("v.items");
            items.push(response.getReturnValue());
            component.set("v.items", items);*/
        }
    });
},
})

```

### campingListForm component

```
<aura:component >
	
        <aura:attribute name="newItem" type="Camping_Item__c"
     default="{ 'sobjectType': 'Camping_Item__c',
                    'Name': '',
                    'Quantity__c': 0,
                    'Price__c': 0,
                    'Packed__c': false }"/>
    <aura:registerEvent name="addItem" type="c:addItemEvent"/>
        <!-- CREATE NEW ITEM FORM -->
    <form class="slds-form--stacked">

      <div class="slds-form-element slds-is-required">
          <div class="slds-form-element__control">
              <ui:inputText aura:id="itemname" label="Name"
                  class="slds-input"
                  labelClass="slds-form-element__label"
                  value="{!v.newItem.Name}"
                  required="true"/>

          </div>
     </div>

     <div class="slds-form-element slds-is-required">
          <div class="slds-form-element__control">
              <ui:inputNumber aura:id="quantity" label="Quantity"
                  class="slds-input"
                  labelClass="slds-form-element__label"
                  value="{!v.newItem.Quantity__c}"
                  required="true"/>

          </div>
      </div>

      <div class="slds-form-element">
          <div class="slds-form-element__control">
              <ui:inputCurrency aura:id="price" label="Price"
                  class="slds-input"
                  labelClass="slds-form-element__label"
                  value="{!v.newItem.Price__c}"
                  />
          </div>
      </div>

      <div class="slds-form-element">
          <ui:inputCheckbox aura:id="packed" label="Packed?"
              class="slds-checkbox"
              labelClass="slds-form-element__label"
              value="{!v.newItem.Packed__c}"/>
      </div>

      <div class="slds-form-element">
          <ui:button label="Create Camping Item"
              class="slds-button slds-button--brand"
              press="{!c.submitForm}"/>
      </div>

    </form>
    <!-- / CREATE NEW ITEM FORM -->
</aura:component>

```

### campingListForm Controller

```
({
    
    submitForm: function(component, event, helper) {    
    if(helper.validateItemForm(component)){
        // Create the new item
        var newItem = component.get("v.newItem");
        helper.createItem(component, newItem);
    }
        
        }

})

```

### campingListForm Helper

```
({
 addItem: function(component, newItem) {
    var addItem = component.getItem("addItem");
    addItem.setParams({ "item": item });
    addItem.fire();
            component.set("v.newItem",{ 'sobjectType': 'Camping_Item__c',
                    'Name': '',
                    'Quantity__c': 0,
                    'Price__c': 0,
                    'Packed__c': false } />);
},
    

		validateItemForm: function(component) {
		
              // Simplistic error checking
        var validItem = true;

        // Name must not be blank
        var nameField = component.find("itemname");
        var itemname = nameField.get("v.value");
        if ($A.util.isEmpty(itemname)){
            validItem = false;
            nameField.set("v.errors", [{message:"Item name can't be blank."}]);
        }
        else {
            nameField.set("v.errors", null);
        }
        
        // Quantity must not be blank
        var quantityField = component.find("quantity");
        var quantity = nameField.get("v.value");
        if ($A.util.isEmpty(quantity)){
            validItem = false;
            quantityField.set("v.errors", [{message:"Quantity can't be blank."}]);
        }
        else {
            quantityField.set("v.errors", null);
        }
		// Price must not be blank
        var priceField = component.find("price");
        var price = priceField.get("v.value");
        if ($A.util.isEmpty(price)){
            validItem = false;
            priceField.set("v.errors", [{message:"Price can't be blank."}]);
        }
        else {
            quantityField.set("v.errors", null);
        }
            return (validItem);

	}
})

```

### addItemEvent.evt

```
<aura:event type="COMPONENT">
    <aura:attribute name="item" type="Camping_Item__c"/>
</aura:event>

```

> Source: https://developer.salesforce.com/forums/?id=906F0000000kDPpIAM
> Need to re-check
