# Form Handling
- Form handling in angular is done with 2 approaches
- Reactive or template based form handling.

---

## Reactive Forms 

### Module file
```javascript
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
    imports:[
        ReactiveFormsModule //Add the reactive form modules to access functionality
    ]
})
```

### Template typescript file
```html
<form [formGroup]="formLogin" (ngSubmit)="onSubmitHandler()">
    <div>
        <input type="text" 
            [formControlName]="'email'" 
            />
        <span *ngIf="!formLogin.get('email').valid && formLogin.get('email').touched">
            Please enter a valid email address!
        </span>
    </div>
</form>
```
- Use email directive to verify if entered is valid email or not
- Use the required directive to ensure that it is filled

### Component Typescript file
```javascript
import { FormGroup, FormControl, Validators } from '@angular/forms';

@Component({...})
export class ComponentName{
    formLogin: FormGroup;

    ngOnInit(){
        this.formLogin = new FormGroup({
            'email': new FormControl(null, [Validators.required, Validators.email])
        });
    }

    onSubmitHandler(){
        //Access the values using formLogin.value
        console.log(this.formLogin.value)
        //Check validity of form by accessing formLogin.valid
        console.log(this.formLogin.valid)
        //Reset values of the form by calling
        this.formLogin.reset()
        //Set values by calling .setValue and .patchValue
        this.formLogin.setValue({ 
            //Set all fields
        })
        this.formLogin.patchValue({
            //Set only fields that you want to
        })
    }
}
```
- Form group allows for grouping multiple controls into a single group.
- Form control (initialState) 

### CSS Styling file
```CSS
.ng-invalid && .ng-touched{
    border: 1px solid red;
}
```

---

## Template based approach

### Module file
```javascript
import { FormsModule } from `@angular/forms`;

@NgModule({
    imports:[
        FormsModule //Pass forms module to the imports
    ]
})
```

### Template file
```html
<form (ngSubmit)="onSubmitHandler(formReference)" #formReference="ngForm">
    <input type="text"
        ngModel
        name="email"
        required
        email
        #emailRef="ngModel"/>
    <span *ngIf="!emailRef.valid">Enter a valid email address</span>
    <button [disabled]="!formReference.valid">Submit</button>
</form>
```
- ngModel implements a two way binding with a variable in component based on the `name` property
- Create a local reference on the form and pass it to the submit method use the `@ViewChild` property.
- `email` and `required` are validators that can be added to validate input. 
- Changing `ngModel` to `[ngModel]="defaultEmail"` where defaultEmail is defined in the component.

### Component file
```javascript
import { NgForm } from '@angular/forms';

@Component({...})
export class ComponentName{
    defaultEmail = "test@email.com";
    formObject = {
        email: ''
    }

    onSubmitHandler(form: NgForm){
        // Access form object here which is same as this.formLogin object in reactive approach
        console.log(form.value)
        this.formObject.email = this.form.value.email;
    }
}
```
```javascript
import { NgForm } from '@angular/forms';
import { ViewChild } from '@angular/core';  //Alternative to using NgForm as an argument passed to submit

@Component({...})
export class ComponentName{
    @ViewChild('formReference') form: NgForm;
    defaultEmail = "test@email.com";
    formObject = {
        email: ''
    }

    onSubmitHandler(){
        // Access form object here which is same as this.formLogin object in reactive approach
        console.log(this.form.value)
        this.formObject.email = this.form.value.email;
        this.form.setValue({
            //Set value for each element
        })
        this.form.pathValue({
            //Set value for required element
        })
        this.form.reset()
    }
}
```
