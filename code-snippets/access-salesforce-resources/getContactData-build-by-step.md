# getContactData: Build by Step

This resource is part of the book [Salesforce Lightning: A Tutorial for Impatient Developers](). This will take you through the incremental process of building Lightning components.



### Add: Data Table

Start with the basics.

#### Apex 

```javascript	
<aura:component implements="flexipage:availableForAllPageTypes, flexipage:availableForRecordHome,force:hasRecordId" access="global" controller="t_getContactDataCon">
```

#### Component


```html

<div>Contact List</div>
    <aura:attribute name="contactList" type="Contact[]"/>

    <!-- data table attributes -->
    <aura:attribute name="data" type="Object"/>
    <aura:attribute name="columns" type="List"/>
    <aura:attribute name="selectedRowsCount" type="Integer" default="0"/>
    <aura:attribute name="maxRowSelection" type="Integer" default="5"/>
    <aura:attribute name="privateMaxRowSelection" type="Integer" default="5" access="PRIVATE"/>
    <aura:attribute name="isButtonDisabled" type="Boolean" default="true" access="PRIVATE"/>
    
    <lightning:button variant="brand" label="Get me some contacts" onclick="{!c.getContacts}"/><br/><br/>
    
    <!-- data table handlers -->
    <aura:handler name="init" value="{! this }" action="{! c.init }"/>
    
    <div class="slds-p-around_medium slds-form slds-form_compound" style="width: 440px;">
        <fieldset class="slds-form-element">
            <div class="slds-form-element__group">
                <div class="slds-form-element__row">
                    <div class="slds-form-element slds-size_1-of-2">
                        <label class="slds-form-element__label">
                            Current row selection length:
                        </label>
                        <div class="slds-form-element__control">
                            {! v.selectedRowsCount }
                        </div>
                    </div>
                    <div class="slds-form-element slds-size_1-of-2">
                        <label class="slds-form-element__label">
                            Current maxRowSelection:
                        </label>
                        <div class="slds-form-element__control">
                            {! v.maxRowSelection }
                        </div>
                    </div>
                </div>
            </div>
        </fieldset>
        <fieldset class="slds-form-element">
            <div class="slds-form-element__group">
                <div class="slds-form-element__row">
                    <div class="slds-form-element slds-size_1-of-2">
                        <lightning:input
                                label="New maxRowSelection:"
                                type="text"
                                value="{! v.privateMaxRowSelection }"
                                onchange="{! c.handleInputChange }"
                                name="maxRowSelection" />
                    </div>
                </div>
            </div>
        </fieldset>
        <div class="slds-form-element">
            <lightning:button
                    label="update maxRowSelection"
                    variant="brand"
                    onclick="{! c.updateMaxRowSelection }"
                    disabled="{! v.isButtonDisabled }"
            />
        </div>
    </div>
    
    <!-- the container element determine the height of the datatable -->
    <div style="height: 300px">
        <lightning:datatable
            columns="{! v.columns }"
            data="{! v.data }"
            keyField="id"
            maxRowSelection="{! v.maxRowSelection }"
            onrowselection="{! c.updateSelectedText }"/>
    </div>
    
    <aura:iteration var="contact" items="{!v.contactList}">
        {!contact.Id}|{!contact.FirstName}|{!contact.FirstName}<br/>
    </aura:iteration>
</aura:component>
```

#### Controller

```json
({
	getContacts : function(component, event, helper) {
        var action = component.get("c.getContactListConFun");
        
        action.setCallback(this, function(response){
			var state=response.getState();
            if (state === "SUCCESS"){
                component.set("v.contactList", response.getReturnValue());
            }
        	else if (state ==="ERROR"){
            	console.log("Error: " + response.getError());
        	}
        })
    	$A.enqueueAction(action);
	},
    
    // data table functions  
    init: function (component, event, helper) {
		component.set('v.columns', [
			{label: 'Id', fieldName: 'contactId', type: 'text'},
        	{label: 'First name', fieldName: 'firstName', type: 'text'},
            {label: 'First name', fieldName: 'lastName', type: 'text'},
        	{label: 'Birthdate', fieldName: 'birthDate', type: 'date'},
            {label: 'Email', fieldName: 'contact', type: 'email'},
            {label: 'Phone', fieldName: 'phone', type: 'phone'},
            {label: 'Address', fieldName: 'mailingAddress', type: 'location'}
        ]);
    },
    
	updateSelectedText: function (component, event) {
        var selectedRows = event.getParam('selectedRows');
        component.set('v.selectedRowsCount', selectedRows.length);
    },
    
    handleInputChange: function (component, event, helper) {
        var value = event.getSource().get('v.value');
        var maxRowSelection = component.get('v.maxRowSelection');
        component.set('v.isButtonDisabled', helper.getButtonState(value, maxRowSelection));
    },
    
    updateMaxRowSelection: function (component) {
        component.set('v.maxRowSelection', component.get('v.privateMaxRowSelection'));
        component.set('v.isButtonDisabled', true);
    }
})
```



#### Helper

```json
({
    getButtonState: function (value, maxRowSelection) {
        return !(value !== "" && this.isPositiveInteger(value) && Number(value) !== maxRowSelection);
    },

    isPositiveInteger: function (value) {
        return /^\d+$/.test(value);
    }
	
})
```

