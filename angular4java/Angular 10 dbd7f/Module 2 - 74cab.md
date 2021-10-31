# Module 2 - Introduction to Typescript

# Chapter 11 - Introduction to Typescript (33m)

50 Module Introduction (2m)

51 Similarities to java and the impact of TSLint (6m)

## 52 Data types and variables (4m)

- number - effectively equivalent to Java *double*
- boolean
- string
- symbol
- null - means the variable has not been given a valid value
- undefined - means the a variable has not been initialized
- Array - like Java *List*
- Tuple - is an Array of fixed size of any type of object
- any - can be used to represent any data type, it can encompass anything at all
- object - can be used to represent any *non-primitive* data type
- void - return type for a method that doesn’t return anything

## 53 Declaring variables (6m)

```tsx
export class AppComponent {
  myNumber: number;
  readonly immutableNumber: number = 42;

  declaringVariablesInMethod(): void {
    let anotherNumber = 0;
    const thirdNumber = 123;

    anotherNumber = 321;
    // anotherNumber = 'Hello'; // error
    // thirdNumber = 321; // error
    console.log('anotherNumber = ', anotherNumber)
    console.log('thirdNumber = ', thirdNumber)
  }
}
```

## 54 Working with Arrays (10m)

```tsx
exploringArrays(): void {
  const myArray1 = new Array<number>(5);        // Java style
  const myArray2 : Number[] = [11, 22, 33, 44]; // JavaScript style

  console.log('myArray1 = ', myArray1);
  console.log('myArray2 = ', myArray2);

  console.log('myArray2[1] = ', myArray2[1]);
  // Slice method
  console.log('myArray2.slice(0, 1) = ', myArray2.slice(0, 1));
  console.log('myArray2.slice(0, 2) = ', myArray2.slice(0, 2));
  console.log('myArray2.slice(0, 3) = ', myArray2.slice(0, 3));
  console.log('myArray2.slice(1, 3) = ', myArray2.slice(1, 3));
  // Splice method
  console.log('myArray2.splice(1, 2) = ', myArray2.splice(1, 2));
  console.log('myArray2 = ', myArray2);
  // Push method
  myArray2.push(55);
  myArray2.push(56);
  console.log('myArray2 = ', myArray2);
  // Pop method
  let pop1 = myArray2.pop();
  console.log('myArray2.pop() = ', pop1);
  let pop2 = myArray2.pop();
  console.log('myArray2.pop() = ', pop2);
}
```

## 55 Loops and conditions (5m)

```tsx
exploringLoopsInJavaStyle(): void {
  const myArray: Number[] = [11, 22, 33, 44];
  for (let i = 0; i < myArray.length; i++) {  // Java style
    console.log('myArray[' + i + '] = ' + myArray[i]);
  }
}
```

```tsx
exploringLoopsInAngularStyle(): void {
  const myArray: Number[] = [11, 22, 33, 44];
  console.log('values:')
  for (const next of myArray) {  // Idiomatic Angular style for values
    console.log('next = ' + next);
  }
  console.log('indexes:')
  for (const index in myArray) {  // Idiomatic Angular style for indexes
    console.log('index = ' + index);
  }
}
```

```tsx
exploringWhileLoops(): void {
  let num = 42;
  while (num > 40) {
    console.log('num = ' + num);
    num--;
  }
}
```

# Chapter 12 - Classes and Objects (32m)

## 56 Understanding JavaScript objects (3m)

```tsx
understandingJavaScriptObjects(): void {
  let myCustomer = {
    firstName: 'Jane Doe',
    age: 29
  }
  console.log('myCustomer = ', myCustomer);
  console.log('typeof myCustomer = ', (typeof myCustomer));
}
```

## 57 Creating classes (5m)

```tsx
export class Book {
  private title: string;
  private author: string;
  private price: number;
}
```

```tsx
creatingClasses(): void {
  const myBook = new Book();
  let myVideo : Video;
}
```

## 58 Class attributes (3m)

```tsx
export class Book {
  readonly id: number = 101;
  title: string;
  author: string;
  private _price: number;

  get price(): number {
    return this._price;
  }

  set price(value: number) {
    if (value > 0) {
      this._price = value;
    } else {
      console.log('Price must be > 0');
    }
  }
}
```

## 59 Constructors and methods (10m)

```tsx
constructor(author: string, title?: string) {
  this.author = author;
  if (title) {  // optional parameter
    this.title = title;
  }
}
```

Typescript methods: *methodName(paramName: paramType): ReturnType*

By default, in Typescript, methods are public.

Arrow functions:

```tsx
exploringArrowFunctions(): void {
  const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  const oddNumbers = numbers.filter(
    (num) => {
      return num % 2 === 1;
    });
  console.log('oddNumbers = ' + oddNumbers);  // oddNumbers = 1,3,5,7,9
}
```

or shorter:

```tsx
const evenNumbers = numbers.filter(num => num % 2 === 0);
```

## 60 String templates (3m)

Using ***backticks*** for string templates:

```tsx
toString(): string {
  return `title: "${this.title}", author: "${this.author}"`;
}
```

## 61 Some hints for debugging classes (3m) (about method toString and console.log)

When using concatenation with strings or string template then method *toString()* is called:

```tsx
const myBook = new Book('James', 'Effective Something');
console.log(`myBook = ${myBook}`);               // toString() is called
console.log('myBook concatenated = ' + myBook);  // toString() is called
console.log(myBook);               // method toString is not called here
console.log('myBook = ', myBook);  // method toString is not called here
```

So using `console.log(’some text’, objectToView);` can be **more useful** for debugging.

If string concatenation is used, but *toString()* is not defined, then console prints *[object Object]* text with no information at all about the object. 

## 62 Angular 10 changes

In the next video we show the difference between == and ===, and we use the example of comparing a string with a number (that is the difference between ‘1’==1 and ‘1’===1). In the latest version of Angular these won’t compile any more. Angular has made a change to the compiler's rules to avoid you making this “mistake” of trying to compare two different object types.

## 63 Object equality (5m)

`==` Abstract equality, trying to cast object to the same variable type

`===` Strict equality, it means *value equality*, there is no real concept of *reference equality* in JavaScript

`!=`

`!==`

```tsx
let myValue: number; // not initialized variable
// @ts-ignore
console.log('myValue === null :' ,myValue === null);            // false
// @ts-ignore
console.log('myValue === undefined :', myValue === undefined);  // true
// @ts-ignore
console.log('myValue == null :' ,myValue == null);              // true
```

# Chapter 13 - Enums (17m)

## 64 Creating an enum (3m)

```tsx
export enum SubjectArea {
  ART, HISTORY, SCIENCE, LITERATURE
}
```

```tsx
console.log(SubjectArea.ART);      // 0
console.log(SubjectArea[0]);       // ART
console.log(SubjectArea.HISTORY);  // 1
console.log(SubjectArea[1]);       // HISTORY
```

## 65 Looping through an enum (5m)

```tsx
const enumArray = Object.keys(SubjectArea);
const enumValuesArray = enumArray.slice(enumArray.length / 2);
for (const value of enumValuesArray) {
  console.log(value);
}
```

This code prints only labels of the SubjectArea enum.

## 66 Angular 10 changes (1m)

In the last video, we saw how to loop through an enum. In recent versions of typescript you’ll get a tslint warning about what we do here, which says that "for-in statements must be filtered with an if statement".

This is a rather odd warning but it’s trying to say “this for loop is likely to not be giving you what you really want – in this case you’re getting both the indices and the values and that’s probably not what you want”. And what they suggest is that you use an if statement inside the for loop.

Now this particular tslint rule really is just requiring an if statement, so in fact the following would remove the warning as far as tslint is concerned.

```tsx
for (const subject in SubjectArea) {
 if (true) {
	 console.log(subject);
 }
}
```

So that gets rid of the warning but it’s not really what we want to do – we normally want to loop through our labels only, and that was covered in the video!

## 67 Enums with values (4m)

We can provide custom values for enum like we might do in Java.

```tsx
export enum CustomSubjectArea {
  ART = 'Arts and Crafts',
  HISTORY = 'History',
  SCIENCE = 'Science and Maths',
  LITERATURE = 'Classic Literature'
}
```

If we are using *enums* with custom values, then looping through labels becomes simpler:

```tsx
for (const value in CustomSubjectArea) {
  console.log(value);  // prints labels of enum
}
// Or to print labels and custom values:
for (const label in CustomSubjectArea) {
  // @ts-ignore
  console.log('label:', label, 
							', custom value:', CustomSubjectArea[label]);
}
```

## 68 Retrieving a label from its value (5m)

Simple and more understandable way:

```tsx
let targetLabel;
for (const label in CustomSubjectArea) {
  // @ts-ignore
  if (CustomSubjectArea[label] === 'Science and Maths') {
    targetLabel = label;
  }
}
console.log('Found target label:', targetLabel);
```

More concise way:

```tsx
const targetValue = 'Science and Maths';
// @ts-ignore
const targetLabel = Object.keys(CustomSubjectArea)
										.find(it => CustomSubjectArea[it] === targetValue);
console.log('Found target label:', targetLabel);
```

# Chapter 14 - Services and Dependency Injection (17m)

## 69 What is a Service (2m)

We’ve seen two types of class in Angular. We have component classes, that’s the code files that act as backing classes to the HTML, and classes that we have created to hold data, classes that represent our model. The component classes are instantiated automatically by Angular when a component comes into view. The custom classes that we’ve created work very much like a Java class in that we can create one or more instances of those classes and each instance will have its own attributes.

However sometimes people want to have a single instance of a class within our project that can be accessed by any component. This will give us a way of sharing data between the components and it can also allow us to provide some kind of useful shared functionality, maybe some utility functions, for example. This idea of a shared class in Angular is called a *service*. It’s really just any class that we can create but with one added feature, and that is that it can be instantiated once automatically and then injected into any of the classes in our application using Dependency Injection. We’re going to let Angular instantiate it and make the single instance it creates available for us to use anywhere that we want to use it.

## 70 Creating a Service (7m)

To generate a service: `ng g s nameOfService`. Angular will add automatically the word “service” to the name we put into command. For example, calling `ng g s data` will generate *DataService* class in file *data.service.ts*:

```tsx
@Injectable({
  providedIn: 'root'
})
export class DataService {
  constructor() { }
}
```

The @Injectable decorator means that this class can be instantiated and injected into other classes within the application. It’s what allows us to use Dependency Injection. The line `providedIn: 'root'` means that this class is going to be available throughout the entirety of our application, from the root of our application and anywhere below it. You could potentially change this to restrict where this class can be used, but that would be an advanced feature that is not going to be covered in this tutorial.

Angular does not need to modify *app.module.ts* file in order the add the newly generated service into it. The only thing that is needed for a service is the file itself with the @Injectable decorator.

We’re going to use the Service as book’s storage now:

```tsx
constructor() {
  this.books = new Array<Book>();
  const book1 = new Book();
  book1.title = 'first book';
  book1.author = 'James';
  book1.price = 2.99;
  this.books.push(book1);
	// ...
}
```

## 71 Angular 10 changes

In the next video, we show you how to reference a service by declaring it in the constructor as follows:

`constructor(dataService: DataService) { }`
This actually won't work now - to be able to reference the service in our 
HTML page, we would need to declare it as a public variable

`constructor(public dataService: DataService) { }`

That is not good practice - you should actually always declare dependency 
injected variables as private, and we will be moving to that very 
soon... this is just for demo purposes!

## 72 Dependency Injection (3m)

The idea is that we want a single instance of our data service class to be used in some of our components. And because this class has the @Injectable decorator, we can use Dependency Injection to get a reference to it.

The way the Dependency Injection works in Angular is we declare the object, not at the class level, but within the parameters of the constructor:

```tsx
constructor(dataService: DataService) { }
```

Defining the object in this way means that our *dataService* is a variable that is available to us anywhere in the class in the same way as a variable declared at the class level would be, but because we’ve put it within the parameters of our constructor, and because this class has the @Injectable decorator, it means that Angular will be responsible for instantiating it for us. Now we should declare this as private variable. That’s important, it’s definitely not good practice to have dependency injected classes publicly available.

```tsx
constructor(private dataService: DataService) { }
```

Now we can use the service in HTML (not a good practice though):

```html
<p>There are {{ dataService.books.length }} books in the database.</p>
```

In order to have access to the *dataService* outside the *DataService* class, we have to make in *public* temporarily:

```tsx
constructor(public dataService: DataService) { }
```

## 73 Exercise 1 - dependency injection

In the Page3 component:

- Inject the *DataService*
- Create a method to remove the last book
- Create a button and bind its click event
- Make sure the method doesn’t crash is the array is empty

## 74 Exercise 1 - solution walkthrough (3m)

```tsx
export class Page3Component implements OnInit {

  constructor(private dataService: DataService) { }

  ngOnInit(): void {
  }

  removeLastBook(): void {
    if (this.dataService.books.length > 0) {
      const book = this.dataService.books.pop();
      console.log('Removed book', book);
    } else {
      console.log('List of books is empty');
    }
  }
}
```

```html
<button (click)="removeLastBook()">
	Delete the last book in the list
</button>
```

# Chapter 15 - The Observer Design Pattern (40m)

## 75 The need for the Observer Design Pattern (10m)

As a general rule you should not be referencing services within the HTML. What is always better is to create a local variable within the Typescript file for the component and to reference that. The target of what we should be doing here is within the class file for our page one component.

If we create a variable that holds number of books:

```tsx
this.books = this.dataService.books;
this.numberOfBooksWrittenByJames = this.books
			.filter(it => it.author === 'James').length;
```

that variable is not recalculated when actual number of books changes. So we need a way to notify the component that some data has been changed so that the variable can be recalculated when needed.

## 76 Introducing the pattern and creating an Observable (5m)

This Design Pattern is used extensively within the internals of Angular. There are often times when we want to use it ourselves. The idea is as follows:

We’ll define an object which is going to be of type *Observable*. An Observable object is a special object type which can send out notifications that we’ll call events. And we can write code that will trigger these events. We’ll then create some further objects which are going to observe these *Observable* objects. We’ll call those the *Observers*. And the Observers will subscribe to receive the notification of the events when they occur. So using this idea, when something changes, our *Observable* can notify the *Observers* that an event has occurred, that some data has changed and the *Observers* can do something about it.

Let’s start by creating an *Observable* variable in the data service. The actual object type that we use in Angular to create an observable is called an *EventEmitter*.

```tsx
bookAddedEvent = new EventEmitter();
```

The *EventEmitter* is a generic type. We can declare it like this which simply means an event has occurred, it doesn’t matter what the event is, we’re just saying “something has happened”. Or we could say that it is an *EventEmitter* with a generic type of *Book:*

```tsx
bookAddedEvent = new EventEmitter<Book>();
```

What this means is that a *Book* event has occurred and actually when the vent occurs, we can send the book data along with the event. So our *observer* objects that are going to subscribe to receive the notifications that our *Book* has been added, can actually be sent the data about the new *Book*.

The alternative way of writing this that has almost exactly the same result but uses a *Subject<Book>* from R*xJS* which is a JavaScript library for reactive programming*.* The recommendation is that you don’t use *Subject* but you always use *EventEmitter* to create an observable. The reason for this is just it is possible that Angular might change things in the future and no longer use RxJS.

## 77 Triggering an event, and creating an Observer (8m)

At the moment we’re adding a book in the *FooterComponent*. It would be sensible rather than calling the book’s push method from here, what we should do is have some encapsulation. Let’s have a *addBook* method in our *DataService* and call that.

```tsx
addBook(book: Book) {
  this.books.push(book);
}
```

Now we want to trigger the event. We call a method called *emit()*. The *emit* method is the method that says “an event has occurred”. Now you can optionally provide a value with this event:

```tsx
addBook(book: Book) {
  this.books.push(book);
  this.bookAddedEvent.emit(book);
}
```

And then the observer can receive that data and see what our new book is without needing to go and query the books array.

So now every time we click on that “Add Book” button on the Footer the event will be fired. We haven’t got anything that is subscribing to the event. The next thing we should do is to subscribe to this *observable*. We’ll create an *observer* to receive these events. We want to do that within the *ngOnInit()* method on Page1 component as that is where we want to be able to look for changes.

First we get a reference to dataService event emitter, and then we’ll call a method *subscribe()* on this event emitter. That is the method that says “we want to receive notifications of these events. Within the parameters of the *subscribe()* method we say what we want to do when the event fires. The first parameter is a lambda expression that will take the data that’s received when the event is fired and define the code that is to be run.

The second parameter has the same format, but it happens when an error occurs. This is optional, you don’t need to provide it.

```tsx
this.dataService.bookAddedEvent.subscribe(
  (newBook) => {
    if (newBook.author === 'James') {
      this.numberOfBooksWrittenByJames++;
    }
  },
  (error) => {
    console.error("Error:", error);
  }
);
```

## 78 Dealing with errors and the complete event (6m)

To help give this some context, we will be using this Design Pattern when we call a REST web service. Suppose we’re calling a service on a remote server to get some data but the back-end doesn’t respond. May be the server is down. In that instance, we won’t get an event occur to say “here’s the data you’re looking for”, we’ll get an error.

So to simulate this as well as firing the event, let’s put in a call to trigger the error. We’ll say that books by author called “James” are not allowed:

```tsx
addBook(book: Book) {
  if (book.author === 'James') {
    this.bookAddedEvent.error('Books by James are not allowed');
  } else {
    this.books.push(book);
    this.bookAddedEvent.emit(book);
  }
}
```

If an error occurs, that actually ends our subscription. Although we can continue adding books and the number of books will be going up, but our subscription has ended. The *observable* is actually still firing the events, but our *observer* has unsubscribed. The subscription has been lost. If you want a subscription to persist, when an error occurs, you actually need to o something in error handling block to regenerate the subscription.

There is a third possible event type which could be fired. We’ve seen *emit* and *error* and the third one is *complete*. What we could do is have a third parameter in *subscribe* method, and actually it’s a parameter that takes no variables, there’ s no data sent when a subscription completes. We could put a lambda there and say “this is the complete event”. And then we could trigger that by calling *complete()* on our event emitter.

```tsx
this.dataService.bookAddedEvent.subscribe(
  (newBook) => {
    if (newBook.author === 'James') {
      this.numberOfBooksWrittenByJames++;
    }
  },
  (error) => {
    console.error("Error when adding book:", error);
  },
  () => {
    console.log("Complete")
  }
);
```

## 79 Unsubscribing an Observer (3m)

You should generally be unsubscribing your observers when you’re finished with them. There are some instances where this doesn’t apply and we’re going to see those in third and fourth modules of this course. But if you’re creating your own event emitters like we are doing here then you should definitely always unsubscribe. The risk of not unsubscribing is that you can potentially create a memory leak. The obvious place to unsubscribe is when we have finished with our component. We’re creating the subscription when our component is initialized in the *ngOnInit* method so we should unsubscribe when our component is destroyed. There is a method that we can hook into that will run when our component is destroyed and that is called *ngOnDestroy*. To be able to use the destroy method we also have to implement the *OnDestroy* interface. We create a variable *subscription* and use it to unsubscribe:

```tsx
subscription: Subscription;

ngOnInit(): void {
	this.subscription = this.dataService.bookAddedEvent.subscribe(
		// ...
	);
}

ngOnDestroy(): void {
  this.subscription.unsubscribe();
}
```

That tidies everything up.

## 80 Exercise 2 - Observer Design Pattern (2m)

Right now the code wok when we add a book, we’re updating the number books. But it’s not working yet when we delete a book. As the exercise we’d like you to try and fix that problem, to make the delete a book method also use an event emitter to ensure that the values get updated correctly.

In the DataService:

- Create a deletedBookEvent
- Create a method to remove the last book
- Call the method from the page 3 component. Throw an error if there are no books to delete
- Subscribe to the deletedBookEvent in page 1
- Subscribe to the event and to errors in the page 3 component. After we clicked on “Delete” button we can find out whether or not that was successful, and we can use an alert to display to the user whether or not the delete worked.

## 81 Exercise 2 - solution walkthrough (7m)

In *DataService*:

```tsx
bookDeletedEvent = new EventEmitter<Book>();

removeLastBook(): void {
  if (this.books.length > 0) {
    const book = this.books.pop();
    this.bookDeletedEvent.emit(book);
  } else {
    this.bookDeletedEvent.error('There are no books left to delete');
  }
}
```

in page 3 component:

```tsx
deleteBookSubscription: Subscription;

ngOnInit(): void {
  this.deleteBookSubscription = this.dataService
																		.bookDeletedEvent.subscribe(
    (book) => {
      alert(`Book written by ${book.author} removed successfully`);
    },
    (error) => {
      alert("Error when removing book: " + error);
    }
  )
}
```

# Chapter 16 - Debugging in Angular (11m)

## 82 Introduction to Debugging (1m)

Both the IDEs IntelliJ and Visual Studio Code support for debugging of your application as long as you’re using a browser such as Chrome. The way we set up the debugger is slightly different in each IDE.

83 Debugging with Visual Studio Code (5m)

## 84 Debugging with IntelliJ (5m)

Toggle Line Breakpoint: Ctrl + F8.

Create debug configuration: Run → Debug... →Edit configurations, then click “+” and choose “JavaScript Debug” option. This will create a default configuration. Then choose the Browser (Chrome) and then insert the URL which our debugger should go to (http://localhost:4200/).

When we call “Debug” (ng serve should be running already) that will open new browser window and open up debug console in IDE. We can change values of the variables either in the console or in the IDEs list of variables.

# Chapter 17 - Unit Testing (45m)

## 85 Creating a Unit Test (6m)

In this chapter we’re going to look at writing unit tests in Angular. Every time we create a component or a service, Angular has created for us some additional files which end in *.spec.ts*. These files contain some dummy tests that Angular creates for us and we can use these files to create our own unit tests. When we tell Angular to run the tests, which we do with the CLI, any spec.ts files with our project will be analysed and any tests contained within them will be run. It’s important to understand that the tests run as a script not in the browser. The application itself doesn’t run. It’s simply each component or service which is being executed in isolation, as a script, against the test files. As a result, this makes it perfect for unit testing. If you’re writing some business logic and you want to use TTD techniques, this feature allows us to do exactly that. Let’s look at some of the code that the CLI has generated for us to get a sense of how the tests are written. We’ll start with app.component.spec file. Angular tests are written using a testing framework called Jasmine. There is a beforeEach method:

```tsx
beforeEach(async () => {
    await TestBed.configureTestingModule({
    declarations: [
      AppComponent
    ],
  }).compileComponents();
});
```

This is the equivalent to JUnit setup method. It’ll run before each test Right now all it does is set up the testing system and load up the various components and services into our application. The methods that are called *it* are actually the test methods:

```tsx
it('should create the app', () => {
  const fixture = TestBed.createComponent(AppComponent);
  const app = fixture.componentInstance;
  expect(app).toBeTruthy();
});
```

In this spec file there are three test methods. One that checks that the app has been created successfully. If we had a compile error in our TypeScript then this test method wouldn’t pass. The other two test methods. They are testing that the application title matches what we expect and the there’s a *span* element which contains the specified text:

```tsx
it(`should have as title 'SimpleAngularApp'`, () => {
  const fixture = TestBed.createComponent(AppComponent);
  const app = fixture.componentInstance;
  expect(app.title).toEqual('SimpleAngularApp');
});

it('should render title', () => {
  const fixture = TestBed.createComponent(AppComponent);
  fixture.detectChanges();
  const compiled = fixture.nativeElement as HTMLElement;
  expect(compiled.querySelector('.content span')?.textContent)
								.toContain('SimpleAngularApp app is running!');
});
```

Like in JUnit these tests will be executed independently. The equivalent to JUNit’s asserts are function calls in the format that we *expect* something and then a method like *toEqual* or *toContain*, and then the expected value at the end.

Let’s write our own tests, so that we can see this in operation. We should do that with our data service. The general syntax for a test method is the word *it*, then some kind of description, and then a comma and a lambda:

```tsx
it('description', () => {
	// ...  
});
```

That’s the outline syntax of what a test method looks like. It will be sensible, on our data service, to test the *addBook* method is doing what we expect. And what we’d expect is that it increases the size of the book array:

```tsx
it('addBook increases the size of the books array', () => {
  const book = new Book();
  service.addBook(book);
  expect(service.books.length).toEqual(4);
});
```

## 86 Running a single test (4m)

To run just one test rename “it” to “fit” and execute `ng test` in CLI. The letter “f” means *focused test*.

To fail the test place into code method *fail()*.

## 87 Test evaluation methods (3m)

- toEqual - is about value equality
- toBe - is about object evuality
- toBeTruthy - means that the vallue is something, which if it can be cast to a boolean, will be evaluated as true
- toBeFalsy - means that the vallue is something, which if it can be cast to a boolean, will be evaluated as false

## 88 Fixing the app component tests (5m)

Add components declarations to *beforeEach* method:

```tsx
beforeEach(async () => {
  await TestBed.configureTestingModule({
    declarations: [
      AppComponent,
      HeaderComponent,
      Page1Component,
      Page2Component,
      Page3Component,
      FooterComponent
    ],
  }).compileComponents();
});
```

Then check query selector in the “should render title” test:

```tsx
expect(compiled.querySelector('h1')?.textContent).toContain('Page 1');
```

If any test fails with message “Error: 1 component threw errors during cleanup” and commenting out the `subscription.unsubscribe();` code solves the problem, then you should place `fixture.detectChanges();` right after `const fixture = TestBed.createComponent(AppComponent);` in the failing test. ([Source](https://stackoverflow.com/questions/43350115/testing-angular-component-with-unsubscribe-error-during-cleanup-of-component))

## 89 Testing behavior (4m)

Testing firing events by event emitter:

```tsx
it('event emitter is firing an event when a book is added', () => {
  spyOn(service.bookAddedEvent, 'emit');
  const book = new Book();
  service.addBook(book);
  expect(service.bookAddedEvent.emit).toHaveBeenCalledWith(book);
});
```

## 90 Testing components with service dependencies (9m)

In Page1Component:

```tsx
it('number of books is incremented correctly', () => {
  const startValue = component.books.length;
  const book = new Book();
  book.author = 'Angular';
  /*
    The structure you need to use to reference a dependency
    injected service in a test method:
    const service = fixture.debugElement.injector.get(ClassOfService)
  */
  const dataService = fixture.debugElement.injector.get(DataService);
  dataService.addBook(book);
  const expectedUpdatedValue = startValue + 1;
  expect(component.books.length).toEqual(expectedUpdatedValue);
});
```

There is an alternative way of doing things that is easier to understand. And that is to manually inject the dataService into the class.

```java
const dataService = new DataService();
```

And then I’m going to replace my component that was defined in *beforeEach* method with a new one where I inject it in.

```java
component = new Page1Component(dataService);
```

So I’m replacing the component that was created in *beforeEach* with a new version where I can define a specific dataService I’m interested in. The other thing we must do is call *ngOnInit* on that component.

```java
const dataService = new DataService();
component = new Page1Component(dataService);
**component.ngOnInit();**
```

So this is an alternative way of testing a service dependency in a component:

```java
it('number of books is incremented correctly (version 2)', () => {
  const startValue = component.books.length;
  const book = new Book();
  book.author = 'Angular';
  const dataService = new DataService();
  component = new Page1Component(dataService);
  component.ngOnInit();
  dataService.addBook(book);
  const expectedUpdatedValue = startValue + 1;
  expect(component.books.length).toEqual(expectedUpdatedValue);
})
```

## 91 Mocking services (13m)

There is another quite useful benefit of this construct where we create our own instance of a service and inject it in ourselves. And that is, let’s suppose our DataService was doing something like making a REST call or accessing some kind of backend system where it was going to be changing some data. We absolutely wouldn’t want that to happen during a test for obvious reasons. We wouldn’t want data to be changed because of a test, and of course, if the backend was down, we wouldn’t want that to be a reason for our test to fail. So in those instances, we wouldn’t want to be testing our Page1Component with the real DataService. What we would do is create a mock version of the DataService, a dummy version that only does the bits we want it to do, and then use that for the purposes of testing.

We’ll want to create a replacement DataService that we’re going to use for testing purposes. It makes sense to put it in the folder called *mocks*. It’s clear then what it is, but it also means we can use it in any of the other components which are going to be wanting to use the same mock file.

```tsx
export class MockDataService {
  books = new Array<Book>();
  bookAddedEvent = new EventEmitter<Book>();
  bookDeletedEvent = new EventEmitter<Book>();

  addBook(book: Book) {}
	removeLastBook() {}
}
```

Now back in our Page1Component, rather than instantiating a new DataService we want a new MockDataService. And then we can inject our MockDataService into the Page1Component. Now we can’t because the data types don’t match. Our MockDataService is not an object of type DataService. We can get around that using interfaces. We change our MockDataService and the underlying DataService to actually be implementations of an interface. We can create the interface in the same class as DataService.

```tsx
export interface DataServiceInterface {
  books: Array<Book>;
  bookAddedEvent: EventEmitter<Book>;
  bookDeletedEvent: EventEmitter<Book>;

  addBook(book: Book): void;
  removeLastBook(): void;
}
```

Now the services should implement the interface:

```tsx
export class DataService implements DataServiceInterface {/*...*/}
export class MockDataService implements DataServiceInterface {/*...*/}
```

In the Page1Component we’re now injecting interface:

```tsx
constructor(private dataService: DataServiceInterface) { }
```

Now the project fails to compile, it couldn’t do the Dependency Injection! We need an extra step to do. That is to use an alias and specify the alias when you’re doing the injecting. We need to put in the *providers* section of app module:

```tsx
providers: [
  {
    provide: 'DataServiceInterface',
    useClass: DataService
  }
],
```

This label also will need to be defined in the test class, because the test classes don’t use app module. We need to add section *providers* into *beforeEach* method:

```tsx

```

And the we need to add a label with @Inject decorator in Page1Component:

```tsx
constructor(@Inject('DataServiceInterface') 
private dataService: DataServiceInterface) { }
```

We’ll need this wherever we’re injecting in the DataServiceInterface.