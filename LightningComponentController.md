# [Lightning Components Basics :: Handle Actions with Controllers](https://trailhead.salesforce.com/modules/lex_dev_lc_basics/units/lex_dev_lc_basics_controllers)

## Mark Item as Packed

Add a button to the campingListItem component that when clicked, marks the item as packed.

* Add a button labeled Packed! that calls the packItem controller function when clicked.
* The controller action marks the item attribute as packed, updates the component's item attribute using the view value provider, and disables the button.

### Lightning Component: campingListItem.cmp

```
<aura:component >
    <aura:attribute name="item" type="Camping_Item__c" 
                    required="true"
                    default="{Name:'Tent', Price__c:100, Quantity__c:1, Packed__c:true}"
                    />
    	<p> The Item is <ui:outputText value ="{!v.item}"></ui:outputText></p>

        <ui:outputText value="{!v.item.Name}"/>
    	<lightning:input type="toggle" label="Packed" name="togglevalue" checked="{!v.item.Packed__c}" />
    	<lightning:formattedNumber value="{!v.item.Price__c}" style="currency" currencyCode="USD" currencyDisplayAs="symbol"/>
    	<lightning:formattedNumber  value="{!v.item.Quantity__c}"/>
    	<lightning:button label="Packed!" onclick="{!c.packItem }"/>
</aura:component>

```

### Controller: campingListItemController.js

```

({
    packItem : function(component, event, helper) {
        var a = component.get("v.item",true);
        a.Packed__c = true;
        component.set("v.item",a);
        btnClicked = event.getSource();         // the button
        //var btnMessage = btnClicked.get("v.label"); // the button's label
        //component.set("v.item.Packed__c", true);
        btnClicked.set("v.disabled", true);
    }
})

```

### Image

![Image](https://github.com/Mulodo-Salesforce-Training/trailhead-salesforce-challenge/blob/master/img/Controllers.png)

> Source: https://developer.salesforce.com/forums/?id=906F0000000kD4mIAE
