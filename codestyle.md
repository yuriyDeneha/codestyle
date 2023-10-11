# Codestyle examples

it's a document to explain the best practice approaches which we should follow to keep a well-written and scalable codebase



### [Minimizing show notification code ](#style-1)
It's a good approach to simplify the interface when it's duplicated and only a few fields are changing from one place to another. 
Also, it's important to do not use static data like strings: `'Success'` or numbers: `3000`

**Bad** ❌:
```typescript
  this.alertService.showNotification({
    severity: 'success',
    summary: 'Success',
    detail: this.translatePipe.callBackTranslate(
      'Quote Note added successfully!'
    ),
    life: 3000,
    sticky: false,
    closable: true
  });
```

**Good** ✅: 
```typescript
  this.alertService.showSuccessNotification(
    NotificationMessage.QUATE_ADDED,
    NotificationDuration.Long
  );
```
     

### [Switch-case-enum approach](#style-2)


It's important to use the Enum approach instead of static string values inside a switch-case block.
Also, a wrong handling delete action can lead to a user experience inconvenience.
Save and delete methods that lead to API calls must be private and protected from direct UI calls.

**Bad** ❌:
```typescript
  switch (requestType) {
    case 'save':
      this.onSave(event);
      break;

    case 'delete':
      if (this.deleteRow) {
        this.onDelete(this.deleteRow);
      }
      break;
    default:
      break;
  }
```

**Good** ✅: 
```typescript
  switch (requestType) {
    case Action.Save: {
      this._save(event);
      return;
    }
    case Action.Delete: {
      if (!this.deleteRow) {
        // handle case: [no entity to delete selected]
      }
      this._delete(this.deleteRow);
      return;
    }
    default: break;
  }
```
     

### [Submiting form](#style-3)

  - Form submitting method should control a data formating from the form and pass it to method which will send to API. Do not overload it with too much code

  - *Why?*: This produces more readable code and avoids too much responsibility on simple submit method. Also it secures private methods which leads to API

**Bad** ❌:
```typescript
onSubmit() {
    let crunchSPO: any = {} as CreateStandardPricing;
    
    if (this.crunchSpoForm.valid) {
      crunchSPO.name = this.crunchSpoForm.value.name;
      (crunchSPO.volume = parseInt(this.crunchSpoForm.value.volume)),
      (crunchSPO.merchantId = this.merchantId)
    }
    this.crunchService.submitSPODetails(crunchSPO).subscribe((result) => {
      if (result.data) {
        this.resetComponent();
        this.sliderService.setIsFieldSaved(true);
        this.crunchService.setCrunchUpdated(true);
      }
    });
}
  ```
**Good** ✅: 

  ```typescript
public formSubmited():void {
    if (this.crunchSpoForm.invalid) {
      // handle invalid form data flow
      return;
    }
    // format the form values inside constructor 
    // with addition class methods if needed
    const crunchSPO = new CreateStandardPricing(this.crunchSpoForm.value);
    
    this._createSPO(crunchSPO);
}

private _createSPO(entity: CreateStandardPricing):void {
    this.crunchService.submitSPODetails(crunchSPO)
      .subscribe((result) => {
        if (!result?.data) {
          // handle negative response
        }
        this._updateUiOnSuccessReponse();
      });
  }

  private _updateUiOnSuccessReponse():void {
    this.resetComponent();
    this.sliderService.setIsFieldSaved(true);
    this.crunchService.setCrunchUpdated(true);
  }
  ```


### [Boolean values](#style-4)

  - the convention for naming Booleans is typically to prefix them with "is", "has" or "can".
  - to do not make complicated names, 
  - better to name in the way to make it `true` as default value
  - avoid: negative or grammatically incorrect & ambiguous words

**Bad** ❌:
```typescript
  inactive: boolean = false; // better to use oppposite word but true by default
  testMode = true; // no am/is/are
  paidAlreadyFor = true; // using prefix at the end, no 'has/have'
  isUsersLoggedIn = true; // grammatically incorrect
  anotherSaveLoadNeeded = false; // too difficult to understand

  ```
**Good** ✅: 

```typescript
  isActive = true;
  isTestModeEnabled = true;
  hasUserPaid = true;
  isEachUserLoggedIn = true;
  canSaveRefresh = true;
```


### [Mock data](#style-5)

  - mocked/test (*.mock.ts) or settings (*.config.ts) data should be moved to seperate file inside the component. Do not keep it inside controller and just import it.

**Bad** ❌:
```typescript
formFieldsArray = [
    {
      id: '0',
      name: 'name',
      text: 'text',
      type: 'type',
      active: true
    },
    {
      id: '1',
      name: 'name1',
      text: 'text1',
      type: 'type1',
      active: false
    },
    {
      id: '2',
      name: 'name2',
      text: 'text2',
      type: 'type2',
      active: true
    },
    ... 
];
  ```
**Good** ✅: 

```typescript
 // create a forms-fields.config.ts file
  export const formFieldsSettingArray: FormField[] = [
    {
      id: '0',
      name: 'name',
      text: 'text',
      type: 'type',
      active: true
    },
    {
      id: '1',
      name: 'name1',
      text: 'text1',
      type: 'type1',
      active: false
    },
    {
      id: '2',
      name: 'name2',
      text: 'text2',
      type: 'type2',
      active: true
    },
    ... 
];
```

### [Enum in conditions](#style-6)


- It's important to use the Enum in conditions with static primitive values: string, number, ....
- Why?: easy to understand, no place for mistakes:
`Admip` instead of `Admin` or `Remove` intead of `Delete`

**Bad** ❌:
```typescript
  commandClick(args: any) {
    if (args.type === 'Delete') {
      ///
    }
    if (args?.notificationLife === 2000) {
      //
    }
    if (args.role == 'agent' || args.role == 'user') {
      //
    }
  }
```

**Good** ✅: 
```typescript
  public commandClicked(args: { 
    type: Commands, 
    notificationLife: NotificationDuration, 
    role: UserRole
  }): void {
      if (args.type === Commands.Delete) {
        ///
      }
      if (args?.notificationLife === NotificationDuration.Long) {
        //
      }
      if ([UserRole.Agent, UserRole.User].includes(args.role)) {
        //
      }
  }
```
     
