# Angular 8

### Setting up an angular project
- Install angular by running `npm install -g @angular/cli`
- `ng new <application_name>` will create a new application.
- Run `ng serve` within the application directory to serve the files

### Creating a component
- Run `ng g c <component_name>` to create a new component under app directory.
- Modify `html` file to define the template for the component and `css` for local stylesheets.
- Every component needs to be defined with the below parameters as an object passed to the `@Component` decorator which decorates the export class definition.
    - `selectors`: `(String)` defines the name of the selector that can be used by other elements to reference in templates
    - `templateUrl`: `(String)` defines the relative url of the template html file for the component
    - `styleUrls`: `(Array(String))` defines an array of stylesheets that needs to be loaded for the component template html file.
- The exported class component should be imported and added to `@NgModule > declarations[]` array so that it is accessible across any components template file.

### Binding HTML to JS
- Angular supports 4 types of binding mainly
    - String Interpolation `{{data}}` 
    - Property binding `[property]="data"` where property is defined on the element and data is a javascript object or value
    - Event binding `(event)="function()"` where defined function is called when event is emitted.
    - Two way data binding `[(ngModel)]="field"` which is used on input elements to have a real time two way data binding between a input element and a javascript string object.

### Directives (Beginner)
- 4 basic directives
    - `*ngIf="<boolean_expression>"` which can be used for structurally changing the availability of an element based on the expression.
    - `[ngStyle]="<property_to_bind>"` which gets an object containing inline styles and the containing css properties. Properties must be converted to camelCase convention.
    - `[ngClass]="<classes_to_bind>"` which gets an object containing classes as keys and boolean values as values stating if a class is to be attached to the element or not.
    - `*ngFor="let obj of listObjectDefined"` to iterate through an object and generate a list
- `[ngSwitch]="property"` can be added to parent element along with `*ngSwitchCase="<val1>"` which means this is a structural directive and can be rendered or removed based on value of property. `*ngSwitchDefault` is added to the default child element that needs to be rendered when no case is matched with parent `ngSwitch` property.

### Custom properties
- To define a property of the class an property that can be set by parent, add the decorator `@Input()` before the property defintion. 
    - Example: `@Input() user: {name: String, id: number}`
- Import `Input` from `@angular/core`.
- To rename the Input property pass a parameter which will acts as an alias `@Input('<alias>')`
- To define a custom event, create an object of an EventEmitter type with an output decorator.
    - Example: `@Output() onUserAdded = new EventEmitter<{object_type}>();`
- Emit the event when necessary by calling .emit()
    - Example: `this.onUserAdded.emit({Object_data_to_emit})`
- Import `Output` and `EventEmitter` from `@angular/core`
- Pass an alias for the Output decorator to change the default name accessible to parents and other elements.

### View encapsulation
- Angular doesn't allow styles from parent elements to be used on the child elements unless they are imported in the styleUrls unlike default CSS.
- By setting the property `encapsulation` on a `@Component`, one can define the type of view encapsulation which can be one of the three.
    - `ViewEncapsulation.None` which means no encapsulation and CSS is imported based on specificity.
    - `ViewEncapsulation.Native` is where the browser supports emulated type natively on its own via ShadowDOM
    - `ViewEncapsulation.Emulated` which is the default where every element gets its own styles irrespective of the parent elements or global styles maintained by angular and not by the browser.

### Local reference
- local references can be defined on elements in the template section by adding a hashtag to the element
    - Example: `<input type="text" #username/>`
- It can only be accessed within the template by passing it as a parameter to any function 
    - Example: `<button (click)="register(username)">Register</button>`
- This passes the above HTML element object as a field which can be accessed in the Javascript function code and data fetched to perform operations.
- Inorder to access the local reference in the javascript, we need to create a `@ViewChild('<local_reference_name>') <property_name>;`
    - Example: `@ViewChild('username') username: ElementRef;` where ElementRef is type of object
- To access the object access the .nativeElement followed by any property.
    - Example: `this.username.nativeElement.value` returns the value of the input.
- It is a acceptable to access the DOM data for reading but do not set the DOM value through the `@ViewChild` object.

### Child elements passed to parent
- `<ng-content></ng-content>` can be added to the current element to render the child elements passed to it.
- Access this in the javascript by defining `@ContentChild('<local_reference>') <propertyName>`

### Lifecycles
- Import the implementations for extending class from `@angular/core`
- `ngOnChanges` called after a bound input property changes
    - class should `implements onChanges`
    - called when event or any change done to the element
    - first call is before the `ngOnInit`
- `ngOnInit`    called once a component is initialized
    - class should `implements onInit`
    - called when object is initialized after the class constructor
- `ngDoCheck`   called during every change detection
    - class should `implements DoCheck`
    - called after constructing a new object for any changes to the object
- `ngAfterContentInit` called after content (ng-content) has been projected into view
    - class should `implements AfterContentInit`
    - called only only once like `ngOnInit` and after `ngDoCheck`
- `ngAfterContentChecked` called every time the projected content has been checked
    - class should `implements AfterContentChecked`
    - called only after `ngAfterContentInit` and every time after `ngDoCheck` for updates
- `ngAfterViewInit` called after the component's view (and child views) has been initialized
    - class should `implements AfterViewInit`
    - called after `ngAfterContentChecked` and only once but not in update cycle
- `ngAfterViewChecked` called every time the view (and child views) have been checked
    - class should `implements AfterViewChecked`
    - called after `ngAfterViewInit` and after `ngAfterContentChecked` in update cycle
- `ngOnDestroy` called once the component is about to be destroyed
    - called during object or element being destroyed 

### Directives (Deep dive)
- Directives of 2 types
    - Attribute (Change attributes of DOM): done via databinding or event binding
    - Structural (Change structure of DOM): looks like an attribute but is preceded with a *
- More than one structural directive can't exist on an element

### Setting up a directive
- import `{ Directive }` from `@angular/core`
- Export a class with `@Directive` decorator added and pass the below properties
    - `selector`: `(String)` which is the name of the selector
- Define a constructor taking an element reference as a parameter
    - Example: `constructor(elemRef: ElementRef){}`. Import ElementRef from core.
- Import this exported class in the `@NgModule` and add it to `declarations` and use it anywhere in the app.
- Also create using `ng g d <directive_name>` which creates under the app module
- Constructor can also be made to take a second parameter which is the `renderer` giving access to rendering stage of Angular. 
    - Example: `constructor(elemRef: ElementRef, renderer: Renderer2){ }`

### HostListener and HostBinding directives
- Define property for class such which will be bound to a specific property of the HTML element using `@HostBinding(<property_name>) <variable_name>`
    - Example: `@HostBinding('style.backgroundColor') backgroundColor = 'transparent'`
    - Remember to use Camel case and also initialize value to basic.
- Change by setting the property value which will change the HTML value
    - Example: `this.backgroundColor = 'blue'`
- Define method `@HostListener('<DOM_Event>') function(){  }` to execute functions which are called when a certain event is emitted on the element where the directive was specified.
    - Example: `@HostListener('mouseenter') mouseover(eventData: Event) }{ this.backgroundColor='blue' }`
    - `mouseenter` is a DOM event triggered on hover of any element
- Using HostBinding can let you skip using renderer!

### Custom Property Binding
- Define properties in the class with the `@Input() propertyName` decorator set. This field makes it accessible to the parent element. Use property binding `[propertyName]="value"` to pass the property to the directive.
- Use the property in the class methods and initialize in `ngOnInit` lifecycle method to initialize before render.

### Services
- Create a service in angular by creating a class in a new file of javascript and import the class.
- Add a `providers: [<service_name>]` for the `@Component({})` which is instantiated by Angular and passed via constructor.
- To fetch by constructor, pass an argument of service_name type to constructor of the export class.
    - Example: `constructor(private <argument_name>: <service_name>)`
