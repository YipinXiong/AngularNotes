[TOC]

# Basic Syntax

- `*ngFor`
- `*ngIf`
- Interpolation `{{ }}`
- Property binding `[ ]`
- Event binding `( )`

## Architecture Overview

![architecture](Notes of Angular.assets/image-20191120141822822.png)

## Two-way binding

```html
<div>
  <label>name:
    <input [(ngModel)]="hero.name" placeholder="name"/>
  </label>
</div>
```

**[(ngModel)]** is Angular's two-way data binding syntax.

Here it binds the `hero.name` property to the HTML textbox so that data can flow *in both directions:* from the `hero.name` property to the textbox, and from the textbox back to the `hero.name`.

> Although `ngModel` is a valid Angular directive, it isn't available by default.
>
> It belongs to the optional `FormsModule` and you must *opt-in* to using it.



## Event types in Angular

```typescript
(click)="myFunction()"      
(dblclick)="myFunction()"   
(submit)="myFunction()"
(blur)="myFunction()"  
(focus)="myFunction()" 
(scroll)="myFunction()"
(cut)="myFunction()"
(copy)="myFunction()"
(paste)="myFunction()"
(keyup)="myFunction()"
(keypress)="myFunction()"
(keydown)="myFunction()"
(input)? where is it from?
(mouseup)="myFunction()"
(mousedown)="myFunction()"
(mouseenter)="myFunction()"
(drag)="myFunction()"
(drop)="myFunction()"
(dragover)="myFunction()"
```



# Components

A component consists of three things:

- **A component class** that handles data and functionality. In the previous section, the product data and the `share()` method in the component class handle data and functionality, respectively.
- **An HTML template** that determines the UI. In the previous section, the product list's HTML template displays the name, description, and a "Share" button for each product.
- **Component-specific styles** that define the look and feel. Though product list does not define any styles, this is where component CSS resides.

> The most different part from React is that components in Angular are more like "MVC"



Components shouldn't fetch or save data directly and they certainly shouldn't knowingly present fake data. They should focus on presenting data and delegate data access to a service.

## Input

Parent components pass their information to child components via `property`. Either way, the `@Input` decorator tells Angular that this property is *public* and available for binding by a parent component. Without `@Input`, Angular refuses to bind to the property.



```typescript
import { Component, OnInit } from '@angular/core';
import { Input } from '@angular/core';
export class ProductAlertsComponent implements OnInit {
  @Input() product;
  constructor() { }

  ngOnInit() {
  }

}
```

## Output

```javascript
import { Component } from '@angular/core';
import { Input } from '@angular/core';
import { Output, EventEmitter } from '@angular/core';
export class ProductAlertsComponent {
  @Input() product;
  @Output() notify = new EventEmitter();
}
```



## Styles

You define private styles either inline in the `@Component.styles` array or as stylesheet file(s) identified in the `@Component.styleUrls` array.

 The Angular [class binding](https://angular.io/guide/template-syntax#class-binding) makes it easy to add and remove a CSS class conditionally. Just add `[class.some-css-class]="some-condition"` to the element you want to style.

```html
<li *ngFor="let hero of heroes"
  [class.selected]="hero === selectedHero"
  (click)="onSelect(hero)">
  <span class="badge">{{hero.id}}</span> {{hero.name}}
</li>
```



## Data binding

![Four ways of data binding](Notes of Angular.assets/image-20191120154527427.png)



## Directives

We integrate the operations about DOM into `directives`. Here are two other kinds of directives: *structural* and *attribute*. In templates, directives typically appear within an element tag as attributes, either by name or as the target of an assignment or a binding.

> You can apply many *attribute* directives to one host element. You can [only apply one](https://angular.io/guide/structural-directives#one-per-element) *structural* directive to a host element.
>
> ?
>
> from Angular - documentation 

### Structural directives

*Structural directives* are responsible for HTML layout. They shape or reshape the DOM's *structure*, typically by adding, removing, or manipulating elements.

- `*ngFor`
- `*ngIf`

### Attribute directives

*Attribute directives* alter the appearance or behavior of an existing element. In templates they look like regular HTML attributes, hence the name.

The `@Directive` decorator's lone configuration property specifies the directive's [CSS attribute selector](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors), `[appHighlight]`.

You use the `ElementRef` in the directive's constructor to [inject](https://angular.io/guide/dependency-injection) a reference to the host DOM element, the element to which you applied `appHighlight`. `ElementRef` grants direct access to the host DOM element through its `nativeElement` property.

```typescript
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {

  constructor(private el: ElementRef) { }

  @Input('appHighlight') highlightColor: string;

  @HostListener('mouseenter') onMouseEnter() {
    this.highlight(this.highlightColor || 'red');
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }

  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

The `@HostListener` decorator lets you subscribe to events of the DOM element that hosts an attribute directive, the `<p>` in this case.

### @input

```typescript
@Input('appHighlight') highlightColor: string;
```

*Inside* the directive the property is known as `highlightColor`. *Outside* the directive, where you bind to it, it's known as `appHighlight`.

You can also add another property in the same `directive` 

Angular knows that the `defaultColor` binding belongs to the `HighlightDirective` because you made it *public* with the `@Input` decorator.



## Dynamic Components

The `<ng-template>` element is a good choice for dynamic components because it doesn't render any additional output.



# NgModule

Every component must be declared in *exactly one* [NgModule](https://angular.io/guide/ngmodules).

*You* didn't declare the `HeroesComponent`. So why did the application work? It worked because the Angular CLI declared `HeroesComponent` in the `AppModule` when it generated that component. Open `src/app/app.module.ts` and find `HeroesComponent` imported near the top.

# Pipes

[Pipes](https://angular.io/guide/pipes) are a good way to format strings, currency amounts, dates and other display data. Angular ships with several built-in pipes and you can create your own.

> Note that the `pipes` here mean the "small directives" used in interpolation to handle string etc. 
>
> In `Observable` section, there is another concept called `pipe`, which combines several operations working on the value returned from observable. 

```html
<h2>{{hero.name | uppercase}} Details</h2>
```

## AsyncPipe

```html
<li *ngFor="let hero of heroes$ | async" >
```

> The `*ngFor` repeats hero objects. Notice that the `*ngFor` iterates over a list called `heroes$`, not `heroes`. The `$` is a convention that indicates `heroes$` is an `Observable`, not an array.

Since `*ngFor` can't do anything with an `Observable`, use the pipe character (`|`) followed by `async`. This identifies Angular's `AsyncPipe` and subscribes to an `Observable` automatically so you won't have to do so in the component class.

# Routing

In Angular, the best practice is to load and configure the router in a separate, top-level module that is dedicated to routing and imported by the root `AppModule`.

## Register URLs

The meta file is `app.module.ts`. Thus, the very first thing is to register the url.

```typescript
//src/app/app.module.ts

@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
    ])
  ],
```



```html
<div *ngFor="let product of products; index as productId">

  <h3>
    <a [title]="product.name + ' details'" [routerLink]="['/products', productId]">
      {{ product.name }}
    </a>
  </h3>
<!-- . . . -->
</div>
```



## Using route info

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { products } from '../products';

export class ProductDetailsComponent implements OnInit {
  product;

  constructor(
  //dependency injection?
    private route: ActivatedRoute,
  ) { }
  
  /* 
  	In the ngOnInit() method, subscribe to route params and fetch the product based 		on the productId. Angular calls ngOnInit() shortly after creating a component. 
  */
  ngOnInit() {
    this.route.paramMap.subscribe(params => {
      this.product = products[+params.get('productId')];
    });
  }
}

```



The `<router-outlet>` tells the router where to display routed views. 

> `<router-outlet>`  is Angular's   `<Switch> <Route />` in React;
>
> `<a routerLink="/heroes> Heros </a>` is the `<Link to="" />` in React-Router-Dom

The `RouterOutlet` is one of the router directives that became available to the `AppComponent` because `AppModule` imports `AppRoutingModule` which exported `RouterModule`.



## Manipulations of Url 

Similarly, in Angular, you need to leverage the `Location` to interact with the URL in the browser.

To get the information appended in the url, you need `ActivatedRoute` from '@angular/router'

```typescript
constructor(
  private route: ActivatedRoute,
  private heroService: HeroService,
  private location: Location
) {}

getHero(): void {
  const id = +this.route.snapshot.paramMap.get('id');
  this.heroService.getHero(id)
    .subscribe(hero => this.hero = hero);
}

goBack(): void {
  this.location.back();
}
```



# Services

## Introduction 

Services are an integral part of Angular applications. In Angular, a service is an instance of a class that can be made available to any part of your application using Angular's [dependency injection system](https://angular.io/guide/glossary#dependency-injection-di).

```typescript
//instance

export class ProductDetailsComponent implements OnInit {
  constructor(
    private route: ActivatedRoute,
    private cartService: CartService
  ) { }
}
```

**Services are the place where you share data between parts of your application.** Removing data access from components means you can change your mind about the implementation anytime, without touching any components. They don't know how the service works.

> Notice that the new service imports the Angular `Injectable` symbol and annotates the class with the `@Injectable()` decorator. This marks the class as one that participates in the *dependency injection system*. 

[Introduction to services and dependency injection](https://angular.io/guide/architecture-services#introduction-to-services-and-dependency-injection)

>  Angular only binds to *public* component properties. 
>
>  That means that if you need to reference some data in a service in your template, you must declare the service as `public` in the constructor. 
>
>  To put it more simplely, if you need to use data from services in the template, you must declare the services as `public` in the constructor. 

## Motivation

Angular distinguishes components from services to **increase modularity and reusability** by separating a component's view-related functionality from other kinds of processing. A component can delegate certain tasks to services, such as fetching data from the server, validating user input, or logging directly to the console. By defining such processing tasks in an *injectable service class*, you make those tasks available to any component. You can also make your app more adaptable by injecting different providers of the same kind of service, as appropriate in different circumstances.



## Registration

To make sure that the `YourService` can provide this service, register it with the *injector*, which is the object that is responsible for choosing and injecting the provider where the app requires it.

By default, the Angular CLI command `ng generate service` registers a provider with the *root injector* for your service by including provider metadata, that is `providedIn: 'root'` in the `@Injectable()` decorator.

```typescript
@Injectable({  providedIn: 'root', })
```

> When you provide the service at the root level, **Angular creates a single, shared instance of `HeroService` and injects into any class that asks for it** (Singleton). Registering the provider in the `@Injectable` metadata also allows Angular to optimize an app by removing the service if it turns out not to be used after all.



# Dependency injection (DI)

You can *inject* a service into a component, giving the component access to that service class.

Some important terminologies of DI:

- The *injector* is the main mechanism. **Angular creates an application-wide injector for you during the bootstrap process, and additional injectors as needed. You don't have to create injectors**.
- An injector creates dependencies, and maintains a *container* of dependency instances that it reuses if possible.
- A *provider* is an object that tells an injector how to obtain or create a dependency.

> For any dependency that you need in your app, you must register a provider with the app's injector, so that the injector can use the provider to create new instances. **For a service, the provider is typically the service class itself.**



When Angular creates a new instance of a component class, it determines which services or other dependencies that component needs by looking at the constructor parameter types. For example, the constructor of `HeroListComponent` needs `HeroService`.

```java
constructor(private service: HeroService) { }		
```

When Angular discovers that a component depends on a service, it first checks if the injector has any existing instances of that service. If a requested service instance doesn't yet exist, the injector makes one using the registered provider, and adds it to the injector before returning the service to Angular.

When all requested services have been resolved and returned, Angular can call the component's constructor with those services as arguments.

The process of `HeroService` injection looks something like this.

![Injector](Notes of Angular.assets/injector-injects.png)

## Providing services

You must register at least one *provider* of any service you are going to use. The provider can be part of the service's own metadata, making that service available everywhere, or you can register providers with specific modules or components. You register providers in the metadata of the service (in the `@Injectable()` decorator), or in the `@NgModule()` or `@Component()` metadata

- By default, the Angular CLI command [`ng generate service`](https://angular.io/cli/generate) registers a provider with the root injector for your service by including provider metadata in the `@Injectable()` decorator. 

  ```typescript
  @Injectable({
   providedIn: 'root',
  })
  ```

  When you provide the service at the root level, Angular creates a single, shared instance of `HeroService` and injects it into any class that asks for it. Registering the provider in the `@Injectable()` metadata also allows Angular to optimize an app by removing the service from the compiled app if it isn't used.

- When you register a provider with a [specific NgModule](https://angular.io/guide/architecture-modules), the same instance of a service is available to all components in that NgModule. To register at this level, use the `providers` property of the `@NgModule()` decorator 

  ```typescript
  @NgModule({
    providers: [
    BackendService,
    Logger
   ],
   ...
  })
  ```

- When you register a provider at the component level, you get a new instance of the service with each new instance of that component. At the component level, register a service provider in the `providers` property of the `@Component()` metadata.

  ```typescript
  @Component({
    selector:    'app-hero-list',
    templateUrl: './hero-list.component.html',
    providers:  [ HeroService ]
  })
  ```



> In short, you can declare the service globally or modulely or locally.





# Observable

## Basic usage and terms

As a publisher, you create an `Observable` instance that defines a *subscriber* function. This is the function that is executed when a consumer calls the `subscribe()` method. The subscriber function defines how to obtain or generate values or messages to be published.

To execute the observable you have created and begin receiving notifications, you call its `subscribe()` method, passing an *observer*. This is a JavaScript object that defines the handlers for the notifications you receive. The `subscribe()` call returns a `Subscription` object that has an `unsubscribe()` method, which you call to stop receiving notifications.

Here's an example that demonstrates the basic usage model by showing how an observable could be used to provide geolocation updates.

```typescript
// Create an Observable that will start listening to geolocation updates
// when a consumer subscribes.
const locations = new Observable((observer) => {
  // Get the next and error callbacks. These will be passed in when
  // the consumer subscribes.
  const {next, error} = observer;
  let watchId;

  // Simple geolocation API check provides values to publish
  if ('geolocation' in navigator) {
    watchId = navigator.geolocation.watchPosition(next, error);
  } else {
    error('Geolocation not available');
  }

  // When the consumer unsubscribes, clean up data ready for next subscription.
  return {unsubscribe() { navigator.geolocation.clearWatch(watchId); }};
});

// Call subscribe() to start listening for updates.
const locationsSubscription = locations.subscribe({
  next(position) { console.log('Current Position: ', position); },
  error(msg) { console.log('Error Getting Location: ', msg); }
});

// Stop listening for location after 10 seconds
setTimeout(() => { locationsSubscription.unsubscribe(); }, 10000);
```

## Observers

A handler for receiving observable notifications implements the `Observer` interface. It is no more than an object that defines callback methods to handle the three types of notifications that an observable can send:

| Notification Type | Description                                                  |
| :---------------- | ------------------------------------------------------------ |
| next              | **Required.** A handler for each delivered value. Called zero or more times after execution starts. |
| error             | **Optional**. A handler for an error notification. An error halts execution of the observable instance. |
| complete          | **Optional.** A handler for the execution-complete notification. Delayed values can continue to be delivered to the next handler after execution is complete. |



## Laziness

If you neglect to `subscribe()`, the service will not send the request to the server. As a rule, an `Observable` *does nothing* until something subscribes.

## Subscribing

You subscribe by calling the `subscribe()` method of the instance, passing an observer object to receive the notifications.

> There is a constructor that you use to create new instances, but for illustration, we can use some methods from the RxJS library that create simple observables of frequently used types:
>
> - `of(...items)`—Returns an `Observable` instance that synchronously delivers the values provided as arguments.
> - `from(iterable)`—Converts its argument to an `Observable` instance. This method is commonly used to convert an array to an observable.

In angular, you can use it as inline functions:

```typescript
myObservable.subscribe(
  x => console.log('Observer got a next value: ' + x),
  err => console.error('Observer got an error: ' + err),
  () => console.log('Observer got a complete notification')
);
```



## Multicasting

A typical observable creates a new, independent execution for each subscribed observer. *Multicasting* is the practice of broadcasting to a list of multiple subscribers in a single execution. With a multicasting observable, re-use the first listener and send values out to each subscriber. When creating an observable you should determine how you want that observable to be used and whether or not you want to multicast its values.

For more details, please visit the official document of Angular website.





## Common operators in RxJS

Note that, for Angular apps, we prefer combining operators with pipes, rather than chaining. Chaining is used in many RxJS examples.

## A concrete instance

>  A substitude to `Promise` in Angular.

To catch errors, you **"pipe" the observable** result from `http.get()` through an RxJS `catchError()` operator. "Pipe" chains many operations one by one.

`tap()` operator, which looks at the observable values, does something with those values, and passes them along. The `tap()` call back doesn't touch the values themselves.

```typescript
/** GET heroes from the server */
getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
    .pipe(
      tap(_ => this.log('fetched heroes')),
      catchError(this.handleError<Hero[]>('getHeroes', [])),
    );
}

const heroes = [{ name: 's', name: 'b' }];
heroes.map(hero => hero.name);
```

`pipe` is similar to `promise` => then, then. That means that the value you get from the url will be **used**, **modified** during the data passing (`pipe`) process. 

> You can "dispatch" the action into a observerable, such as adding a new activitiy. Then, it will update for you automatically.

>  Observable is a "stream". There is another dimension called "time". As time goes by,  there will probably be many values flowing into the "stream". 

## Subject

A `Subject` is both a source of observable values and an `Observable` itself. You can subscribe to a `Subject` as you would any `Observable`. You can also push values into that `Observable` by calling its `next(value)` method as the `search()` method does.

```java
private searchTerms = new Subject<string>();

// Push a search term into the observable stream.
search(term: string): void {
  this.searchTerms.next(term);
}

this.heroes$ = this.searchTerms.pipe(
  // wait 300ms after each keystroke before considering the term
  debounceTime(300),

  // ignore new term if same as previous term
  distinctUntilChanged(),

  // switch to new search observable each time the term changes
  switchMap((term: string) => this.heroService.searchHeroes(term)),
);
```

Each operator works as follows:

- `debounceTime(300)` waits until the flow of new string events pauses for 300 milliseconds before passing along the latest string. You'll never make requests more frequently than 300ms.

- `distinctUntilChanged()` ensures that a request is sent only if the filter text changed.

- `switchMap()` calls the search service for each search term that makes it through `debounce()` and `distinctUntilChanged()`. It cancels and discards previous search observables, returning only the latest search service observable.

  > `switchMap()` preserves the original request order while returning only the observable from the most recent HTTP method call. Results from prior calls are canceled and discarded.

  ## Compared to other techniques

  [compare to other techniques](https://angular.io/guide/comparing-observables)
  
  ## Httpclient
  
  An alternative to Axios in Angular

# Forms in Angular

There are two parts to an Angular Reactive form, the objects that live in the component to store and manage the form, and the visualization of the form that lives in the template.

Both reactive and template-driven forms share underlying building blocks.

- `FormControl` tracks the value and validation status of an individual form control.

  > Personally speaking, every input value is an instance of FormControl.

- `FormGroup` tracks the same values and status for a collection of form controls.

  > Just as a form control instance gives you control over a single input field, a form group instance tracks the form state of a group of form control instances (for example, a form)

  ```html
  <form [formGroup]="profileForm">
    <label>
      First Name:
      <input type="text" formControlName="firstName">
    </label>
    <label>
      Last Name:
      <input type="text" formControlName="lastName">
    </label>
  </form>
  ```

  Note that just as a form group contains a group of controls, the *profile form* `FormGroup` is bound to the `form` element with the `FormGroup` directive, creating a communication layer between the model and the form containing the inputs. The `formControlName` input provided by the `FormControlName` directive binds each individual input to the form control defined in `FormGroup`. The form controls communicate with their respective elements. They also communicate changes to the form group instance, which provides the source of truth for the model value.

- `FormArray` tracks the same values and status for an array of form controls.

- `ControlValueAccessor` creates a bridge between Angular `FormControl` instances and native DOM elements.



## Ref input's value 

```html
<div>
  <label>Hero name:
    <input #heroName />
  </label>
  <!-- (click) passes input value to add() and then clears the input -->
  <button (click)="add(heroName.value); heroName.value=''">
    add
  </button>
</div>
```

Use **#** to reference the dom?



## Reactive Form

*Reactive forms* provide a model-driven approach to handling form inputs whose values change over time.

> With reactive forms, the **`FormControl` instance** always returns a new value when the control's value is updated. (Immutable)

```typescript
import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-reactive-favorite-color',
  template: `
    Favorite Color: <input type="text" [formControl]="favoriteColorControl">
  `
})
export class FavoriteColorComponent {
  favoriteColorControl = new FormControl('');
}
```

In reactive forms, the form model is [the source of truth](https://en.wikipedia.org/wiki/Single_source_of_truth). In the example above, the form model is the `FormControl` instance.

### Data flow 

As described above, in reactive forms each form element in the view is directly linked to a form model (`FormControl` instance). Updates from the view to the model and from the model to the view are synchronous and aren't dependent on the UI rendered. 

![data-flow-reactive-form](README.assets/dataflow-reactive-forms-vtm.png)

#### from view to model.

1. The user types a value into the input element, in this case the favorite color *Blue*.
2. The form input element emits an "input" event with the latest value.
3. The control value accessor listening for events on the form input element immediately relays the new value to the `FormControl` instance.
4. The `FormControl` instance emits the new value through the `valueChanges` observable.
5. Any subscribers to the `valueChanges` observable receive the new value.

![Reactive forms data flow - model to view](README.assets/dataflow-reactive-forms-mtv.png)

#### from model to view.

1. The user calls the `favoriteColorControl.setValue()` method, which updates the `FormControl` value.
2. The `FormControl` instance emits the new value through the `valueChanges` observable.
3. Any subscribers to the `valueChanges` observable receive the new value.
4. The control value accessor on the form input element updates the element with the new value.

> Differentiate the model to view and view to model because you need to consider other components who subscribe the "change" observable, that is formControl. 



### Creating nested form groups

Using a nested form group instance allows you to break large forms groups into smaller, more manageable ones. *Form groups can accept both form control and form group instances as children.* 

```typescript
import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  selector: 'app-profile-editor',
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
  profileForm = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
    address: new FormGroup({
      street: new FormControl(''),
      city: new FormControl(''),
      state: new FormControl(''),
      zip: new FormControl('')
    })
  });
}
```

Even though the `address` element in the form group is a child of the overall `profileForm` element in the form group, the same rules apply with value and status changes. Changes in status and value from the nested form group **propagate to the parent form group**, maintaining consistency with the overall model.

### Partial model updates

There are two ways to update the model value:

- Use the `setValue()` method to set a new value for an individual control. The `setValue()` method strictly adheres to the structure of the form group and replaces the entire value for the control.
- Use the `patchValue()` method to replace any properties defined in the object that have changed in the form model.

### FormBuilder - Service

The `FormBuilder` service has three methods: `control()`, `group()`, and `array()`. These are factory methods for generating instances in your component classes including form controls, form groups, and form arrays. Use the `group` method to create the `profileForm` controls.

```typescript
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';

@Component({
  selector: 'app-profile-editor',
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
  profileForm = this.fb.group({
    firstName: [''],
    lastName: [''],
    address: this.fb.group({
      street: [''],
      city: [''],
      state: [''],
      zip: ['']
    }),
  });

  constructor(private fb: FormBuilder) { }
}
```

Use the `group()` method with the same object to define the properties in the model. The value for each control name is *an array* containing the initial value as the first item in the array.

> **Note:** You can define the control with just the initial value, but if your controls need sync or async validation, add sync and async validators as the second and third items in the array.





# Lifecycle Hooks

## ngOnChanges

*ngOnChanges*: Called every time *a data-bound input property* changes. It’s called a first time before the **ngOnInit** hook. The hook receives a *SimpleChanges* object that contains the previous and current values for the data-bound inputs properties. This hook gets called often, so it’s a good idea to limit the amount of processing it does.

```typescript
import { Component, Input, SimpleChanges, OnChanges }
  from '@angular/core';

@Component({
  // ...
})
export class MyTodoComponent implements OnChanges {
  @Input() title: string;
  @Input() content: string;

  constructor() { }

  ngOnChanges(changes: SimpleChanges) {
    for (let property in changes) {
      if (property === 'title') {
        console.log('Previous:', changes[property].previousValue);
        console.log('Current:', changes[property].currentValue);
      }
    }
  }
}
```



## ngOnInit

Called once upon initialization of the component.

## ngOnDestroy

Called once when the component is destroyed and a good hook to use to cleanup and unsubscribe from observables.

## ngDoCheck

Since `ngOnChanges` only detects `@input` properties, it makes it possible to check other changes.  





# Typescript

[typescript in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)



# RxJS

[Introduction to RxJS](https://www.youtube.com/watch?v=ei7FsoXKPl0)

[Visualization of RxJS](https://rxmarbles.com/#race)

# Self-comments

In Angualr, it uses `directives` to change the default behaviors of html tags.



In Angualr, you need to take advantage of decoraters(@) to configure your application. Some component level metadata is configured via `@Component`. The most important `@NgModule` decorator annotates the top-level **AppModule** class.

> Angular needs to know how the pieces of your application fit together and what other files and libraries the app requires. This information is called *metadata*.
>
> Some of the metadata is in the `@Component` decorators that you added to your component classes. Other critical metadata is in [`@NgModule`](https://angular.io/guide/ngmodules) decorators.
>
> The Angular CLI generated an `AppModule` class in `src/app/app.module.ts` when it created the project.

Decoraters are used to inform Angular of what part of this `class` belongs to.  

