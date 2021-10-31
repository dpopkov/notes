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

`<h1>{{ pageName }}</h1>`

32 Template expressions are dynamic (4m)

## Chapter 7 - Simple event binding

### 33 Angular 10 changes

Declare *explicit* return type for all methods. If the method doesn’t return anything then declare return type as *void*:

```tsx
onButtonClick(): void {
}
```

### 34 Event binding (6m)

Template interpolation is a way to get information from code into HTML. The next thing we’d like to look at is *event binding*. This is the ability to invoke Typescript methods from the HTML. The idea is that rather than binding to a property or a variable in our code file, we are going to be creating a method in our code file and will bind the method to an event, which can occur when the HTML runs.

35 Exercise 2 - event binding (3m)

36 Exercise 2 - solution walkthrough (2m)

## Chapter 8 - Component interaction - reading properties

### 37 Using the hidden HTML attribute (5m)

Selective Display: on any HTML element in Angular, there are two ways that we can choose whether or not we want to display it. We can either use an Angular feature, or we could use the simple HTML property - *hidden*: `<div hidden>`

38 Angular 10 changes

### 39 Accessing properties of a child component (7m)

To bind an HTML attribute we use *square* brackets. The we give header component a name - give *template reference*. It is a name preceded with *hash symbol* - #.

```html
<app-header #headerComponent></app-header>
<app-page1 [hidden]="headerComponent.pageRequested !== 1"></app-page1>
<app-page2 [hidden]="headerComponent.pageRequested !== 2"></app-page2>
<app-page3 [hidden]="headerComponent.pageRequested !== 3"></app-page3>
```

40 The ***ngIf** structural directive (5m)

## Chapter 9 - Component interaction - property binding

### 41 Setting up the scenario (5m)

In this chapter, we’re going to continue working with the idea of a parent component accessing a child’s properties, and then in the next chapter we’ll make this two-way, that is allowing the child to read or set a property in it’s parent component.

Let’s now try and set the value of a property in the child component. We’ll start by doing this in the code files. So we’ll start by learning how to read and write child’s properties from the parent’s class file.

Scenario is this:

We need to create a property in one of the child components. We’ll pick the footer this time.

We’re going to create a button on the parent component, which will update the value in this property of the child.

### 42 Accessing properties from code (7m)

First declare an object that matches the correct type for the child, then give the child a template reference in the HTML, and then use the @ViewChild decorator to connect the two together:

```html
<app-footer #footer></app-footer>
```

```tsx
@ViewChild('footer')
footerComponent: FooterComponent;

updateLastAccessed(): void {
  this.footerComponent.lastAccessed = new Date().toString();
}
```

### 43 The @Input Decorator (6m)

We’re going to write to a child property from the HTML.

In the *AppComponent* we create a local variable `startTime` that will initialize on the `ngOnInit()` method:

```tsx
startTime: string;

ngOnInit(): void {
  this.startTime = new Date().toString();
}
```

In the HTML where we define the footer we want to set the value of property lastAccessed. We bind to lastAccessed by putting it in square brackets in the HTML and applying the `@Input()` decorator in *FooterComponent* code file:

```html
<app-footer #footer [lastAccessed]="startTime"></app-footer>
```

```tsx
@Input()
lastAccessed = '';
```

## Chapter 10 - Component interaction - event binding

### 44 Why we need custom events (5m)

At the end of chapter 8 we explained that sometimes using ***ngIf** to show or hide a component or rather to include it or exclude it from the DOM can sometimes be problematic. And in this chapter we’ll understand why by learning a bit more about how components can interact with each other.

It’s time to see how a child can access the properties and methods of a parent component. Let’s imagine that you want to have a hit counter for one of our pages. Let’s start by creating the hit counter within page two.

Text not finished ...

### 45 Creating an event emitter (7m)

What we’re now going to be doing is creating our own events to bind to. When the user clicks on one of links in the header component and we’re currently running the method *onPageChange()*, within this method we’re going to write some code that says we need to fire a new event at this point. May be we call it *pageChangedEvent*. In the same way that HTML buttons have a *click* event, which is a hook we can pick up in code and react to by saying some methods should run whenever that event occurs, well, our new *pageChangedEvent* will also be a hook that we can react to by running some code whenever that event occurs.

So to use the correct language we can create event, and then bind to that event and run some code every time the event takes place.

So that we can bind to the new *pageChangedEvent* of our header, we need to create this within the header component as an event and apply the `@Output` decorator to make this event bindable from outside of the component:

```tsx
@Output()
pageChangedEvent = new EventEmitter();
```

We use `@Input` decorator to allow a parent to set a child property in HTML. It means this property is something that’s going to be set externally. `@Output` says that this is going to be sending out an event. It’s going to be sending data outside of this particular component and something, probably the parent, is going to be listening out for that output event. So by having the `@Output` decorator we can then bind to this event just like we did the *click* event.

We know that the header component how has an event that we can bind to:

```html
<app-header (pageChangedEvent)="incrementHitCounter()"></app-header>
```

When our *pageChangeEvent* occurs we want to increment our hit counter. That’s a method on the page 2 component. We can reference *Page2Component* within our *AppComponent*. We want our *pageChangeEvent* to call local *incrementHitCounter()* method within the *AppComponent*.

Now we only have to trigger that *pageChangeEvent*.

### 46 Triggering an event (2m)

To trigger call *emit()* method:

```tsx
this.pageChangedEvent.emit()
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

To finish this chapter, we should look again at the difference between using ***ngIf** and **hidden** attribute to choose whether or not to display a component within our page. The things aren’t going to quite work correctly if we use *ngIf. 

If we revert back to *ngIf then page 2 always has zero hits. Our hit counter appears to be broken. The thing is that our *page2Component* is defined using @ViewChild decorator. But the *page2Component* may not always be in the DOM because we’re using *ngIf which changes structure of the DOM. That is the difference between *ngIf and *hidden* to do selective display. You can run into difficulties, if using *ngIf you’re trying to access data within the components. As a general rule, if you’re using *template references* do not use *ngIf on the same object, because if you do, that *template reference* may not always exist.

Now we leave this simple project. 

# Module 2 - Introduction to Typescript

- Typescript basics

[Module 2 - Introduction to Typescript](Angular%2010%20dbd7f/Module%202%20-%2074cab.md) 

# Module 3 - Building a real site

*Start of Case Study.*

- Functioning Angular front-ends

# Module 4 - Connecting to a REST back end

after

# Module 5 - Security and Deployment

- Login
- Angular security
- REST security