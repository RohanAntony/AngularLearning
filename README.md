# Angular 8

### Setting up an angular project
- Install angular by running `npm install -g @angular/cli`
- `ng new <application_name>` will create a new application.
- Run `ng serve` within the application directory to serve the files

### Creating a component
- Run `ng g c <component_name>` to create a new component under app directory.
- Modify `html` file to define the template for the component and `css` for local stylesheets.
- Every component needs to be defined with the below parameters as an object passed to the `@Component` decorator which decorates the export class definition.
    - `selector`: `(String)` defines the name of the selector that can be used by other elements to reference in templates
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
- Adding a service to providers creates a new instance of the service, inorder to use a single instance, define the service in the parent component and only pass it to the constructor in child components. This means, add it to `providers:[]` and pass as parameter to `constructor` in parent component but only pass as argument to `constructor` in child component which will use the same instance as the parent.
- By adding the service to `providers` in `@NgModule` at the highest level, the same instance can be passed to all components as well as services by creating a constructor in the service and passing an argument of the required service. This class although now should be added with a decorator of `@Injectable()` which can be imported from `@angular/core`
- Services should be used as abstractions for data logic with data being stored in services. Create an `EventEmitter<type>()` object which can be called with object when data needs to be sent and subscribed to to recieve.
    - Example: `statusUpdate = new EventEmitter<string>();`
    - `this.<servicename>.statusUpdate.emit(status)` on the emitting end
    - `this.<servicename>.statusUpdate.subscribe( (status) => { \\perform operations } )` on recieving end

### Routing
- Import `Router` and `RouterModule` from `@angular/router` in the app module folder.
- Define a const named routes which is an array of objects containing {path: , component: } which maps a component for a path 
    - Example: `const appRoutes: Routes = [ { path: '', component: HomeComponent } , { path: 'users', component: UsersComponent } ]`
- Add `RouterModule.forRoot(appRoutes)` in the `imports` section of the module to register the routes.
- Inorder to render the routes and their respective component, add the directive `<router-outlet></router-outlet>` in the template where the multiple components share a common space.
- For the links that are to be clicked to load a component, replace the `href="path"` with `routerLink="path"` so that page refresh doesn't occur when the link is clicked. Page refresh results in the application reloading and loss of data if not saved. RouterLinks use the concept of property binding.
- RouterLinks can be used on any component and use relative path if defined without a leading `/` and absolute path if defined with leading `/`
    - Example: `<a routerLink="/users">` goes to the users page from base path whereas `<a routerLink="users">` goes to users page from current path
- To activate the selected tab add `routerLinkActive="<class_name>"` to all tabs that can be activated. This states angular to add the class to the element when the given tab is active.
- Use `[routerLinkActiveOptions]="{exact: true}"` on base paths to add the class defined in `routerLinkActive` only when exact path is set. 

### Routing via Typescript
- To use routes in component class inject the component constructor arguments first with `private router: Router`.
- Use the `this.router.navigate(['/path', '<subpaths>'])` to navigate to given page.
- To get the current route in Component, inject constructor with `private route: ActivatedRoute` and access `this.route` to fetch the current route.
- Parameters can be passed to the route in App module by defining ` {path: 'users/:id', component: UserComponent} `. The ':' states to angular that whatever follows users is to be taken as an id.
- To access the id inject the constructor with `private route: ActivatedRoute` and access `this.route.snapshot.params['id']` to fetch the data.
- Angular doesn't update component if the route changes with different data as it should reload the page which is inefficient. Inorder to fetch any changes to the data in the URL, use `this.route.params.subscribe( (params: Params) => { //update the params object } )`
- When a component is destroyed, we need to destroy the subscription added by calling unsubscribe in the ngOnDestroy.
    - To do this, import `{ Subscription }` from `rxjs/Subscription`. Angular uses this module and is shipped by default.
    - Store the subscription defined above in a variable of the type `paramsSub: Subscription` and `this.paramsSub = this.route.params.subscribe()`
    - Call `unsubscribe` on `paramsSub` in `ngOnDestroy`. `this.paramsSub.unsubscribe()`

### QueryParams in routes
- By adding property to any link in template named `[queryParams]="{field: value, field2: value2}"`, it gets converted during runtime to `?field=value&field2=value2`
- By setting property `[fragment]="'loading'"` or `fragment="loading"` to a link, we define the fragment that needs to be appended to the URL which would result in the URL ending with `#loading`
- To pass programmatically in `.navigate()`, set the second param of the function call with an object containing `{queryParams : {field: value}, fragment: value2}`
    - Example: `this.router.navigate(['/users', id, 'edit'], {queryParams: {allowEdit: true}, fragment: 'editSection'})`
- To retrieve this data, inject the constructor with `private route: ActivatedRoute`.
- Access `this.route.snapshot.queryParams` or `this.route.snapshot.fragment` to access data and also add `this.route.queryParams.subscribe()`
and `this.route.fragment.subscribe()` to listen to changes.

### Child Routing
- To implement child routing, add `<router-outlet></router-outlet>` in the component where the child templates need to be loaded.
- In the module where routes are defined, add a field ``
    - Example: 
    ```json
    {   path: 'users', 
        component: UsersComponent, 
        children: [ 
            { path: ':id', component: UserComponent }, 
            { path: ':id/edit', component: EditUserComponent} 
        ]
    }
    ```

### Redirecting and passing static data
- To redirect create a path object in module directory with path set to `**` and second field `redirectTo: '<path>'`. Make sure this route is set last in
the list else, it will override any other routes as it is a wilcard route.
    - Example: `{ path: '**', redirectTo: '/' }`, component field is not required as it doesn't render a component and instead redirects to home path
- To the same path object also pass `{data: {message: 'Data passed'} }` and use the injected `private route: ActivatedRoute` via `this.route.snapshot.data['message']`.For changes one can subscribe on `this.route.data.subscribe()`

### Observables
- Use the event emitter and subscribe pattern.
- Subscribe to the observable and obtain a handle in `ngOnInit()` and unsubscribe the observable in `ngOnDestroy` to avoid multiple observables from being created everytime. This is to be done for custom observable only, angular defined observables are destroyed by angular when component goes out of scope.
    - Example: `this.subscription = <Observable>.subscribe( fn => { //perform operations } )` in `ngOnInit`
    - whearas `this.subscription.unsubscribe()` to be called in `ngOnDestroy`
- To create a custom observable, import `Observable` from `rxjs`.
- Create an instance of `const customObservable = Observable.create(observer => {  })` which creates a custom observable that can be subscribed to.
    - `observer` passed to the inner function has 3 methods `.next(<data>)`, `.complete()` and `.error()` to pass intermediate data, complete or throw error.
    - an observable stops execution when either `.complete()` or `.error()` is called. To catch errors pass second function to `.subscribe()` and to check for successful execution pass third function to `.subscribe()` like `customObservable.subscribe(data => {//called for .next}, error => {//called for error}, () => {//called for .complete})`
    - `customObservable.unsubscribe()` can be called to unsubscribe to event.

### Forms
- Refer formHandling.md for more details.
- Validators shipped with Angular can be referred to at [Validators](https://angular.io/api/forms/Validators)
- Adding a `[(ngModel)]="<field_name>"` allows for a two way binding 
- For dynamic inputs in form 
    - Create property in formGroup with type formArray. `'field': new FormArray([])`
    - Push elements to this array by calling `(<FormArray>this.signupForm.get('field')).push(new FormControl(null, Validators.required))`.
    - Link the template for a parent div that shows this element by adding `[formArrayName]="'field'"`.
    - Use a `*ngFor="let fields of (<FormArray>signupForm.get('field')).controls; let i = index"` to display each element
- Form errors can be obtained from `formLogin.get('email').errors['required']`
- Listen to value changes  using `this.signupForm.valueChanges.subscribe( value => {} )`
- Listen to status changes using `this.signupForm.statusChanges.subscribe( status => {} )`

### Pipes
- Used to transform data at the last step on template layer. Used in template with a preceding `|`. 
    - Example `{{ username | uppercase }}` where username is converted to uppercase
- `| date:'fullDate' ` can be used to format a date field and show full date.
- Parsing of data is done from left to right and ordering along with matching input from previous pipe to current is important.

### Custom pipes
- Create a new file with the name `<name>.pipe.ts` and import `PipeTransform` and `Pipe` from `@angular/core`.
- Add this file to `declarations` in `@NgModule`.
- Export a class which `implements PipeTransform` and implements the `transform(value: any, [optional additional arguments]){}`. 
- Also add the decorator `@Pipe` for the class exported and pass an object with `name` field set.
- To pass a parameter, pass it as second argument to `transform`
- Example: 
```javascript
@Pipe({
    name: 'shorten'
})
export class ShortenPipe implements PipeTransform{
    transform(value: any, limit: number, start:number){
        return value.substr(start, limit);
    }
}
```
- Use this in the template by calling `| shorten:10:4` where 10 is the limit and 4 is the start.
- to create a pipe using ng, run `ng g p <pipe_name>`

### HTTP 
- Import `HttpClientModule` from `@angular/common/http` and add it to `imports` in `@NgModule`.
- Inject `HttpClient` from `@angular/common/http` in the component constructor via parameter.
- Use `this.http.post()` or `this.http.get()` to make the request.
- First param is URL, second param is the data and returns an observable which needs to be subscribed to.
    - Example: `this.http.post(url, data).subscribe(responseData => {})`
- Use `.pipe(responseData => {}) after .post()` to convert response data 
- To add a loading indicator, use a variable `loading` which is set to false initially and set it to `true` after the request until the `.subscribe` is executed.
- Implement services to use Http and avoid running HTTP request in components. For async operations, return the `this.http.get().pipe()` method which would let the Component call `.subscribe()` and perform operations on final data as required.
- Use `this.http.delete(url)` to delete content on the backend.
- The second argument of `.subscribe()` can be used to handle `error`. Add a field to the component which is set to false and display a template only when the error is set to true along with message which is set to another field.

### Advanced HTTP
- Import `HttpHeaders`, `HttpParams` from `@angular/common/http`.
- Set second parameter of `.get()` to `{headers : new HttpHeaders({ 'Custom-Header': 'Hello' })}` or third parameter in `.post()`
- Also set `params: new HttpParams().set('field', 'value')` after setting `headers` to add query params in the string.
- For multiple parameters, call `.append('field', 'value')` in a chain like
    - Example: `let queryParams = new HttpParams().append('field1', 'v1').append('f2', 'v2')` and pass `queryParams` to `params:` in the object.
- For fine grain control such as running methods after each event of http, set `{ observe: events }` and call the `.pipe(events => {})` where events is the event passed which can be `Sent`, `ResponseHeader`, `Response`, `DownloadProgress`, `UploadProgress` and so on. Use this to define what functionality is to be executed during which event.
- Angular by default converts the response object to `json` type and can be explicitly set to `text` or `blob` for file upload or string response using `responseType: 'blob'` property.

### Http Interceptor
- Interceptors are intermediate functions that can be used for adding data which is custom but always required in every request like authentication.
- Import `HttpInterceptor`, `HttpRequest` and `HttpHandler` from `@angular/common/http` and define a class which is to be exported and `implements HttpInterceptor`. 
- This class must implement the function `intercept(req: HttpRequest<any>, next:HttpHandler)` which should return `next.handle(req)`. Make any required changes on `req` object.
- Add this to the `providers` in `@NgModule` by adding an object like `providers: [{ provide: HTTP_INTERCEPTOR, useClass: <exportedClass>, multi: true }]`
    - `multi` means multiple services can be instantiated
- `req` object here is immutable and create a new copy and make changes to the new copy and pass it to the `handle`
    - Example: `const modReq = req.clone({ headers: req.headers.append('Auth', 'xyz') })`
- Extending the `.handle()` with a `.pipe(tap(event => {}))` allows for intercepting response also. Compare `event.type === HttpEventType.Response` and access `event.body` if true. import `tap` from `rxjs/operators`
- With multiple interceptors, order the interceptors in `providers` as per which needs to be run first.

### Authentication
- Create an auth component class along with a template with selector set to `app-auth` and add it to `@NgModule -> declarations` 
- Create a single form which can switch state between `login` and `register`
- Create a new service `AuthService` which has below methods and add it to `providers` in `@NgModule`
    - `constructor`: Inject HttpClient to this method.
    - `signUp`: Use the Http object to make a request to the backend and fetch the token.
- Inject the `AuthService` in the component `constructor` and call the methods where required.
- Create a new `User` class that is to be exported with 2 methods and 4 properties
    - `email`, `id`, `_token`, `_tokenExpirationDate` as properties
    - `get token()` returns token by default except if `_tokenExpirationDate` is in the past, returns null
- Implement an interceptor called `AuthInterceptorService` which `implements HttpInterceptor` for adding token to every request.
- Handle the case for login and signup where there is no token by simply sending the request forward as is instead of adding any parameter. Add this check by checking if there is a user object created or if `isAuthenticated` is true.
- For logout, add a method in the service which will set the `user` object to null. 
- Use local storage to store the token by calling `localStorage.setItem('userData', JSON.stringify(user))` and define a function called `autoLogin()` which access the storage, checking for `'userData'` and if not available return null else load `JSON.parse(localStorage.getItem('userData'))` and return a new user created.
- Implement autoLogout using a `setTimeout` with duration set to `expirationTime`. Also, capture the handle and call `clearTimeout(handle)` in logout to avoid calling logout twice if user manually logs out.
- To avoid access to pages from URL routing, use `CanActivate` from `@angular/router`. 
    - Implement a class which `implements CanActivate` and should define a function `canActivate` that takes 2 params and returns either 
        - `route: ActivatedRouteSnapshot`
        - `router: RouterStateSnapshot`

### Modules
- Seperate Components, Directives and Services into seperate modules known as feature module.
- Inorder to create a module, add `@NgModule` decorator to the exported class and pass `imports` and `exports` for the components that you would want to import and export. This makes the `AppModule` much smaller in definition. Add `CommonModule` for access to `ngForOf` or other angular directives. DO NOT load `BrowserModule` a second time. 
- Transfer all the routing to another module and use `redirectTo` to delegate.
- Multiple declarations are not allowed!!'

### Deployment of Angular App
- Add changes necessary for production environment in `environment.prod.ts` and development environment in `environment.ts`. This already comes with angular-cli under `src/environment`.
- import value from files into `{ environment }`
- Run `ng build --prod` to generate static files which 
- Point reverse proxy server to serve `index.html` under `dist/<folder_name_containing_index.html>`

### Testing
- Run `ng test` to run tests.

### Angular Universal

### NgRx

### Angular Animations

