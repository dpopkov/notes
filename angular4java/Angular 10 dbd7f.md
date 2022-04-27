# Angular 10 hands on

Chapter 1 - Introduction

# Module 1 - Creating a simple angular app

- install Angular
- setup IDE
- build 1st site

## Chapter 2 - What is Angular?

4 How Angular sites differ from traditional web technologies (5m)

5 How Angular can create a fast, responsive use experience (3m)

6 What an Angular application looks like (4m)

7 How to get support while you’re taking this course (2m)

## Chapter 3 - Setting up the environment

8 The software needed to build with Angular (2m)

9 Installing Node.js (1m)

10 Installing the Angular CLI (2m)

11 Testing Angular with a hello world application (6m)

12 Angular 10 changes (1m)

13 Running the hello world application (2m)

14 Configuring the Intellij IDEA (3m)

15 Installing and configuring Visual Studio Code (4m)

## Chapter 4 - Introducing Angular architecture

16 An overview of our first example site (3m)

17 What is a component (2m)

18 index.html and styles.css (2m)

19 Component selectors (3m)

20 The 4 component files (4m)

21 How Angular can construct a page (5m)

22 Angular doesn’t assemble the HTML (7m)

### 23 How to open a project from the sample files (5m)

`npm install`      # will install dependencies and populate node_modules folder

`npm audit fix`   # downloades the latest versions and fixes vulnerabilities

## Chapter 5 - Creating a component

### 24 Creating a new  project (2m)

`ng new SimpleAngularApp`

### 25 Running the project (3m)

To start the project type in command line: `ng serve`

### 26 Creating the component with the CLI tool (5m)

command: `ng generate component ComponentName`

or: `ng g c ComponentName`

It creates component and changes *app.module.ts* which contains declarations and paths for all components.

27 Editing the Component’s HTML and making it appear (2m)

28 Exercise 1 - creating components (2m)

29 Exercise 1 - solution walkthrough (2m)

## Chapter 6 - Template Expressions

30 An overview of the Component Class structure (8m)

### 31 Template expressions (3m)

Template interpolation simply means that we can have a variable in our TypeScript file and we can reference that variable from our HTML or we can bind part of our HTML to a property in the backing class.

`<h1>{{ pageName }}</h1>`

### 32 Template expressions are dynamic (4m)

If we were to change the value of the variable *pageName* in our code, the HTML would be automatically updated to see this updated variable value.

```tsx
ngOnInit(): void {
    // This callback proves that template expressions are dynamic
    setTimeout(() => { this.pageName = 'First page' }, 5000);
}
```

## Chapter 7 - Simple event binding

### 33 Angular 10 changes

Declare *explicit* return type for all methods. If the method doesn’t return anything then declare return type as *void*:

```tsx
onButtonClick(): void {
}
```

### 34 Event binding (6m)

Template interpolation is a way to get information from code into HTML. The next thing we’d like to look at is *event binding*. This is the ability to invoke Typescript methods from the HTML. The idea is that rather than binding to a property or a variable in our code file, we are going to be creating a method in our code file and will bind the method to an event, which can occur when the HTML runs. For example a button click might be an event that would want to trigger some code that we write.

HTML: `<button (click)="onButtonClick()">Greet me</button>`

```tsx
onButtonClick(): void {
  alert('Hello - the date today is ' + new Date());
}
```

35 Exercise 2 - event binding (3m)

### 36 Exercise 2 - solution walkthrough (2m)

HTML:

```html
<div class="header">
  <div class="link"><a (click)="onPageChange(1)">Page 1</a></div>
  <div class="link"><a (click)="onPageChange(2)">Page 2</a></div>
  <div class="link"><a (click)="onPageChange(3)">Page 3</a></div>
</div>
```

Typescript (only changes value of the variable *pageRequested*):

```tsx
pageRequested = 1;

onPageChange(page: number): void {
  this.pageRequested = page;
  console.log('Changed page to', this.pageRequested);
}
```

## Chapter 8 - Component interaction - reading properties

### 37 Using the hidden HTML attribute (5m)

Selective Display - choosing whether or not to display a part of a page, based on some kind of dynamic variable or expression that we need to calculate.

On any HTML element in Angular, there are two ways that we can choose whether or not we want to display it. We can either use an Angular feature, or we could use the simple HTML property - *hidden*: `<div hidden>`

The **concept** of using *hidden* (it is not working solution but just an illustration) in app.component.html:

```html
<app-page1 hidden="pageRequested != 1"></app-page1>
<app-page2 hidden="pageRequested != 2"></app-page2>
<app-page3 hidden="pageRequested != 3"></app-page3>
```

### 38 Angular 10 changes

When using @ViewChild decorator the 2nd parameter ({static: true}) is no longer needed.

### 39 Accessing properties of a child component (7m)

To *bind* an HTML attribute we use *square* brackets. Then we give header component a name - give *template reference*. It is a name preceded with *hash symbol* - #.

Version 1 of *the selective display* in the app.component.html using now the *hidden* attribute and the variable *pageRequested* in a child component:

```html
<!-- Give the child component a template reference -->
<app-header #headerComponent></app-header>
<!-- Use template reference and bound HTML attribute -->
<app-page1 [hidden]="headerComponent.pageRequested !== 1"></app-page1>
<app-page2 [hidden]="headerComponent.pageRequested !== 2"></app-page2>
<app-page3 [hidden]="headerComponent.pageRequested !== 3"></app-page3>
```

### 40 The ***ngIf** structural directive (5m)

There is an alternative way of achieving the same thing. In the 2nd version we’re going to use a structural directive of Angular called ***ngIf**. 

```html
<!-- Give the component a template reference -->
<app-header #headerComponent></app-header>
<!-- Use template reference and *ngIf structural directive -->
<app-page1 *ngIf="headerComponent.pageRequested === 1"></app-page1>
<app-page2 *ngIf="headerComponent.pageRequested === 2"></app-page2>
<app-page3 *ngIf="headerComponent.pageRequested === 3"></app-page3>
```

With the hidden attribute, the elements are always in the DOM. With ***ngIf** we’re not saying to hide or show the selectors, we’re saying include them or exclude them from the DOM.

There will be instances where ***ngIf** can cause a problem.

## Chapter 9 - Component interaction - property binding

### 41 Setting up the scenario (5m)

In this chapter, we’re going to continue working with the idea of a parent component accessing a child’s properties, and then in the next chapter we’ll make this two-way, that is allowing the child to read or set a property in it’s parent component.

Let’s now try and set the value of a property in the child component. We’ll start by doing this in the code files. So we’ll start by learning how to read and write child’s properties from the parent’s class file.

Scenario is this:

We need to create a property in one of the child components. We’ll pick the footer this time.

Typescript: `lastAccessed = '';`

HTML: `<p>Last accessed: {{ lastAccessed }}</p>`

We’re going to create a button on the parent component, which will update the value in this property of the child.

app.component.html:

```html
<app-footer #footer></app-footer>
<button (click)="updateLastAccessed()">Update last accessed</button>
```

app.component.ts:

```tsx
@ViewChild('footer')
footerComponent: FooterComponent;

updateLastAccessed(): void {
  console.log('Button "Update last accessed" was clicked');
  console.log('The previous was', this.footerComponent.lastAccessed)
  let lastAccessed = new Date().toString();
  this.footerComponent.lastAccessed = lastAccessed;
  console.log('lastAccessed updated to', lastAccessed)
}
```

### 42 Accessing properties from code (7m) - ViewChile decorator

First declare an object that matches the correct type for the child, then give the child a template reference in the HTML, and then use the @ViewChild decorator to connect the two together. Code in app.component.ts:

```tsx
@ViewChild('footer')  // footer - template reference in HTML
footerComponent: FooterComponent;

updateLastAccessed(): void {
  this.footerComponent.lastAccessed = new Date().toString();
}
```

Template reference (*footer*) in app.component.html:

```html
<app-footer #footer></app-footer>
```

### 43 The @Input Decorator (6m) - writing to property from HTML

We’re going to write to a child property from the HTML.

In the *AppComponent* we create a local variable `startTime` that will initialize on the `ngOnInit()` method:

```tsx
startTime: string;

ngOnInit(): void {
  this.startTime = new Date().toString();
}
```

In the HTML where we define the footer we want to set the value of property lastAccessed. We bind to lastAccessed by putting it in square brackets in the HTML and applying the `@Input()` decorator in *FooterComponent* code file.

app.component.html:

```html
<app-footer #footer [lastAccessed]="startTime"></app-footer>
```

footer.component.ts:

```tsx
@Input()
lastAccessed = '';
```

## Chapter 10 - Component interaction - event binding

### 44 Why we need custom events (5m) - child accessing parent

At the end of chapter 8 we explained that sometimes using ***ngIf** to show or hide a component or rather to include it or exclude it from the DOM can sometimes be problematic. And in this chapter we’ll understand why by learning a bit more about how components can interact with each other.

So far we have seen how a parent component can interact with a child component. This is a one way interaction. The parent can read or amend a property of a child.

Now it’s time to see how a child can access the properties and methods of a parent component. Let’s imagine that you want to have a hit counter for one of our pages. Let’s start by creating the hit counter within page two.

page2.component.ts:

```tsx
hits = 0;

incrementHitCounter(): void {
  this.hits++;
}
```

page2.component.html:

```html
<p>This page has had {{ hits }} hits</p>
```

We want to amend the variable *hits* by calling this *incrementHitCounter()* method every time the page is displayed. The determination of when page2 is displayed is something that is initiated in the header component. We have a method *onPageChange(page: number)* in it which is run whenever the links are clicked on. And if we look at the content of that method, that’s currently setting the local variable *pageRequested*. 

```tsx
onPageChange(page: number): void {
  this.pageRequested = page;
  console.log('Changed page to', this.pageRequested);
}
```

What we’d really like to do in this method is somehow access the *incrementHitCounter()* method of our page2 component whenever the method is setting *pageRequested* equal to 2.

You can see that this isn’t going to be completely straightforward. The method we want to call is in the page2 component. But the *onPageChange()* method takes place in the header component. The header component and the page2 component are both children of the app component, but they have on relationship with each other. They are siblings, but they can’t see each other or communicate with each other.

The reason for that is clear. There’s a relationship between app and header, because the selector for the header appears in the HTML for the app component. And there’s a relationship between page2 and app, because the selector for the page2 appears in the HTML for the app component. But there’s no selector for the header in the page2 component, and there’s no selector for page2 in the header component.

In order to communicate between header and page2 we’ll need to find some kind or root between them, and that will therefore involve the app component. We’ll need to get a message from the header to the app component and from the app component to the page2 component. The second part - communicating between app and page2 - can be done using property binding, because that is a parent talking to a child. But the first part - communicating from header to app, that’s from the child to a parent - needs something a little different. A child can never see the property or method of it’s parent. 

There is a different model that we use. The way that this works is that the child can send a message to it’s parent. It can tell the parent that something has happened, and the parent can determine what it wants to do as a result of receiving that message.

In our example the header component can send a message to the app component. The correct terminology here actually is that this is an **event**. We say that the child has an **event** which can occur and the parent can listen our to these **events**. Actually the fact that this is parent-child relationship becomes irrelevant. The idea is that on component can be sending out events and any other component within our application can be listening tor those events. In fact potentially multiple places could be listening for those events.

### 45 Creating an event emitter (7m)

What we’re now going to be doing is creating our own events to bind to. When the user clicks on one of links in the header component and we’re currently running the method *onPageChange()*, within this method we’re going to write some code that says we need to fire a new event at this point. May be we call it *pageChangedEvent*. In the same way that HTML buttons have a *click* event, which is a hook we can pick up in code and react to by saying some methods should run whenever that event occurs, well, our new *pageChangedEvent* will also be a hook that we can react to by running some code whenever that event occurs.

So to use the correct language we can create event, and then bind to that event and run some code every time the event takes place.

We could bind to the click event of a button because HTML’s have an *onclick* event. So that we can bind to the new *pageChangedEvent* of our header, we need to create this within the header component as an event. To do that we declare the variable in our header component of type **EventEmitter**. We need to do something extra to make this event bindable from outside of the component. We apply the `@Output` decorator to make this event bindable.

```tsx
@Output()
pageChangedEvent = new EventEmitter();
```

We use `@Input` decorator to allow a parent to set a child property in HTML. It means this property is something that’s going to be set externally. `@Output` says that this is going to be sending out an event. It’s going to be sending data outside of this particular component and something, probably the parent, is going to be listening out for that output event. By having the `@Output` decorator we can then bind to this event just like we did the *click* event or the *mouseenter* event.

We know that the header component how has an event that we can bind to:

```html
<app-header (pageChangedEvent)="incrementHitCounter()"></app-header>
```

When our *pageChangeEvent* occurs we want to increment our hit counter. That’s a method on the page2 component. We can reference *Page2Component* within our *AppComponent*. We want our *pageChangeEvent* to call local *incrementHitCounter()* method within the *AppComponent*.

app.component.ts:

```tsx
@ViewChild('page2Component')
page2Component: Page2Component;

incrementHitCounter(): void {
  this.page2Component.incrementHitCounter();
}
```

app.component.html:

```html
<app-header (pageChangedEvent)="incrementHitCounter()"></app-header>
<app-page1 [hidden]="currentPage !== 1"></app-page1>
<app-page2 [hidden]="currentPage !== 2" #page2Component></app-page2>
<app-page3 [hidden]="currentPage !== 3"></app-page3>
```

Now we only have to trigger that *pageChangeEvent*.

### 46 Triggering an event (2m)

To trigger call *emit()* method in header.component.ts:

```tsx
onPageChange(page: number): void {
  this.pageRequested = page;
  console.log('Changed page to', this.pageRequested);
  this.pageChangedEvent.emit();
}
```

The hit counter will be incrementing too much, because we haven’t done anything that says this is only to work on page two. It’s currently working on every link that we click on.

### 47 Sending data with events (3m)

The hit counter is increasing by too much. It’s recording every page change. What we could do is send out some information along with the event. And the information could be which page has been occurred. Event emitter is actually a generic type. So what we could say is, it’s an event emitter, and the data type is going to be *number* with a small n:

```tsx
@Output()
pageChangedEvent = new EventEmitter<number>();
```

Now when we send out the page changed event, we can actually provide a number along with it, that would be the current page:

```tsx
this.pageChangedEvent.emit(page);
```

And then when we bind to this event, that’s in the *AppComponent* the page changed event is going to get some data, and to access this data, we have a special syntax which is “dollars event”:

```html
<app-header (pageChangedEvent)="incrementHitCounter($event)"></app-header>
```

And then we can edit our method *incrementHitCounter()* in the *AppComponent*, to take the page number as a variable:

```tsx
incrementHitCounter(page: number): void {
  if (page === 2) {
    this.page2Component.incrementHitCounter();
  }
}
```

We only had to rollback selective display of pages to use hidden attribute:

```html
<app-page1 [hidden]="headerComponent.pageRequested !== 1"></app-page1>
```

At this point everything will now be working.

### 48 Revising *ngIf and hidden (5m)

So, everything is now working, and there’s a little bit of tidying up that we could do. If we look at the *AppComponent* HTML, we’re currently referencing a variable of a child of the *headerComponent*. We don’t need to do that anymore, because, when that page changes, we’ve got an event bound to it, which means that our *AppComponent* knows which is the correct page. So it’s better to have a variable in the *AppComponent* to store what is the current page. And when our increment hit counter method runs, what we could do in here is update this current page:

```tsx
incrementHitCounter(page: number): void {
	this.currentPage = page;
  if (page === 2) {
    this.page2Component.incrementHitCounter();
  }
}
```

Having done that, it means that in the HTML we can reference this variable *currentPage* and we no longer need to look at the header component. And that means we can remove the template reference for the header component:

```html
<app-header (pageChangedEvent)="incrementHitCounter($event)"></app-header>
<app-page1 [hidden]="currentPage !== 1"></app-page1>
<app-page2 [hidden]="currentPage !== 2" #page2Component></app-page2>
<app-page3 [hidden]="currentPage !== 3"></app-page3>
```

To finish this chapter, we should look again at the difference between using ***ngIf** and **hidden** attribute to choose whether or not to display a component within our page. The things aren’t going to quite work correctly if we use ***ngIf**. 

If we revert back to *ngIf then page2 always has zero hits. Our hit counter appears to be broken. The thing is that our *page2Component* is defined using `@ViewChild` decorator. But the *page2Component* may not always be in the DOM because we’re using ***ngIf** which changes structure of the DOM. That is the difference between ***ngIf** and *hidden* to do selective display. You can run into difficulties, if using ***ngIf** you’re trying to access data within the components. As a general rule, if you’re using *template references* do not use ***ngIf** on the same object, because if you do, that *template reference* may not always exist.

Now we leave this simple project. 

# Module 2 - Introduction to Typescript

- Typescript basics

[Module 2 - Introduction to Typescript](Angular%2010%20dbd7f/Module%202%20-%2074cab.md) 

# Module 3 - Building a real site

*Start of Case Study.*

- Functioning Angular front-ends

[Module 3 - Building a real site](Angular%2010%20dbd7f/Module%203%20-%2053cca.md)

[Module 3 - Building a real site (part2)](Angular%2010%20dbd7f/Module%203%20-%20ca1a0.md)

# Module 4 - Connecting to a REST back end

[Module 4 - Connecting to a REST backend](Angular%2010%20dbd7f/Module%204%20-%209f58c.md)

[Module 4 - Connecting to a REST backend (part2)](Angular%2010%20dbd7f/Module%204%20-%203c4ad.md)

# Module 5 - Security and Deployment

- Login
- Angular security
- REST security

[Module 5 - Security and Deployment (part1)](Angular%2010%20dbd7f/Module%205%20-%2000141.md)

[Module 5 - Security and Deployment (part2)](Angular%2010%20dbd7f/Module%205%20-%20a532d.md)