# [Lightning Components Basics :: Attributes and Expressions](https://trailhead.salesforce.com/modules/lex_dev_lc_basics/units/lex_dev_lc_basics_attributes_expressions)

## Create a Packing List Item Component

Create a Lightning Component to display a single item for your packing list.

* Create a component called campingListItem.
* Add an attribute named item of type Camping_Item__c that is required.
* Display Name, Price, Quantity, Packed using an expression.
* Display Price and Quantity using the appropriate formatted number fields.
* Display Packed status using a toggle.

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
</aura:component>

```

### Create New Custom Object

![Image](https://github.com/Mulodo-Salesforce-Training/trailhead-salesforce-challenge/blob/master/img/Attributes.png)

> Source: https://salesforce.stackexchange.com/questions/191276/lightning-components-basics-attributes-and-expressions-trail
