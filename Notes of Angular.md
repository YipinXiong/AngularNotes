[TOC]

# Basic Syntax

- `*ngFor`
- `*ngIf`
- Interpolation `{{ }}`
- Property binding `[ ]`
- Event binding `( )`

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

Parent components pass their information to child components via `property`



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



# NgModule

Every component must be declared in *exactly one* [NgModule](https://angular.io/guide/ngmodules).

*You* didn't declare the `HeroesComponent`. So why did the application work?

It worked because the Angular CLI declared `HeroesComponent` in the `AppModule` when it generated that component.

Open `src/app/app.module.ts` and find `HeroesComponent` imported near the top.

# Pipes

[Pipes](https://angular.io/guide/pipes) are a good way to format strings, currency amounts, dates and other display data. Angular ships with several built-in pipes and you can create your own.

```html
<h2>{{hero.name | uppercase}} Details</h2>
```

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

Services are the place where you share data between parts of your application. Removing data access from components means you can change your mind about the implementation anytime, without touching any components. They don't know how the service works.

> Notice that the new service imports the Angular `Injectable` symbol and annotates the class with the `@Injectable()` decorator. This marks the class as one that participates in the *dependency injection system*.

[Introduction to services and dependency injection](https://angular.io/guide/architecture-services#introduction-to-services-and-dependency-injection)

>  Angular only binds to *public* component properties. 
>
> That means that if you need to reference some data in a service in your template, you must declare the service as `public` in the constructor.

## Motivation

Angular distinguishes components from services to **increase modularity and reusability** by separating a component's view-related functionality from other kinds of processing. A component can delegate certain tasks to services, such as fetching data from the server, validating user input, or logging directly to the console. By defining such processing tasks in an *injectable service class*, you make those tasks available to any component. You can also make your app more adaptable by injecting different providers of the same kind of service, as appropriate in different circumstances.



## Registration

To make sure that the `YourService` can provide this service, register it with the *injector*, which is the object that is responsible for choosing and injecting the provider where the app requires it.

By default, the Angular CLI command `ng generate service` registers a provider with the *root injector* for your service by including provider metadata, that is `providedIn: 'root'` in the `@Injectable()` decorator.

```typescript
@Injectable({  providedIn: 'root', })
```

> When you provide the service at the root level, **Angular creates a single, shared instance of `HeroService` and injects into any class that asks for it** (Singleton). Registering the provider in the `@Injectable` metadata also allows Angular to optimize an app by removing the service if it turns out not to be used after all.



## Httpclient

An alternative to Axios in Angular



## Observable

A substitude to `Promise` in Angular.

To catch errors, you **"pipe" the observable** result from `http.get()` through an RxJS `catchError()` operator.

`tap()` operator, which looks at the observable values, does something with those values, and passes them along. The `tap()` call back doesn't touch the values themselves.

```typescript
/** GET heroes from the server */
getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
    .pipe(
      tap(_ => this.log('fetched heroes')),
      catchError(this.handleError<Hero[]>('getHeroes', []))
    );
}
```

`pipe` is similar to `promise` => then, then. That means that the value you get from the url will be **used**, **modified** during the data passing (`pipe`) process. 

# Forms in Angular

There are two parts to an Angular Reactive form, the objects that live in the component to store and manage the form, and the visualization of the form that lives in the template.



Similarily, forms in Augular should be controlled by developers as React does.



```typescript
// There is a built-in package of form-controll in Angular, a service called "FormBuilder".

export class CartComponent {
  items;
  checkoutForm;

  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
  ) {
    this.items = this.cartService.getItems();

    this.checkoutForm = this.formBuilder.group({
      name: '',
      address: ''
    });
  }
}
```





```html
<!-- Use a formGroup property binding to bind the checkoutForm to the form tag in the template. Also include a "Purchase" button to submit the form. -->

<h3>Cart</h3>

<p>
  <a routerLink="/shipping">Shipping Prices</a>
</p>

<div class="cart-item" *ngFor="let item of items">
  <span>{{ item.name }} </span>
  <span>{{ item.price | currency }}</span>
</div>

<form [formGroup]="checkoutForm" (ngSubmit)="onSubmit(checkoutForm.value)">

  <div>
    <label for="name">
      Name
    </label>
    <input id="name" type="text" formControlName="name">
  </div>

  <div>
    <label for="address">
      Address
    </label>
    <input id="address" type="text" formControlName="address">
  </div>

  <button class="button" type="submit">Purchase</button>

</form>
```



# Typescript

[typescript in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)

# Self-relfection

In Angualr, it uses `directives` to change the default behaviors of html tags.



In Angualr, you need to take advantage of decoraters(@) to configure your application. Some component level metadata is configured via `@Component`. The most important `@NgModule` decorator annotates the top-level **AppModule** class.

> Angular needs to know how the pieces of your application fit together and what other files and libraries the app requires. This information is called *metadata*.
>
> Some of the metadata is in the `@Component` decorators that you added to your component classes. Other critical metadata is in [`@NgModule`](https://angular.io/guide/ngmodules) decorators.
>
> The Angular CLI generated an `AppModule` class in `src/app/app.module.ts` when it created the project.