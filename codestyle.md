# Codestyle examples

it's a document to exmplain the best practice approches which we should follow to keep a well-written and scalable codebase



## Minimizing show notification code 

It's a good approach to simplify interface when it's duplicated and only a few fields are changing from one place to another. 
Also, it's important to do not use static data like strings: `'Success'` or numbers: `3000`

**Bad** ❌:

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

**Good** ✅: 
   
     this.alertService.showSuccessNotification(
	     NotificaitonMessage.QUATE_ADDED,
	     NotificaitonDuration.Long
	 );
     

## Switch-case-enum approach

It's important to use Enum approach instead of static string values inside a switch-case block.
Also, a wrong handling delete action can lead to a user experience inconvenience.
Save and delete methods which lead to API calls must be private and pretected from direct UI calls.

**Bad** ❌:

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

**Good** ✅: 
   
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
     
