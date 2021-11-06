# Module 3 - Building a real site

# Chapter 18 - Using external Resources

## 93 Introducing the case study (6m)

Introducing Spring Boot ‘Roombook’ application that uses Thymeleaf.

## 94 Using Bootstrap in our project (6m)

We create new application: `ng new BookingClient` , with no Routing and CSS styling. Then we copy Bootstrap links from `bootstrap.html` in backend project. We could past these links directly into *head* section of our `index.html` file, but we’re going to use an alternative way. The problem with referencing the files in index.html is that it means that our computer must be online in order for those files to be accessed. If we look at where those files are located, we’ll see they are provided on servers that are part of content delivery networks, so our application or our computer must be online, connected to the Internet, in order to be able to retrieve these files.

## 95 Installing Bootstrap using NPM

Sometimes we might be coding when we don’t have a valid Internet connection. Maybe when we’re travelling for example. So we’d like as an alternative to be able to get and use a local copy of all the files that Bootstrap needs. If you’ve used Bootstrap before you know that you can absolutely download and save locally a copy of these files, and if we did that, we put those files somewhere in our project. The *assets* folder is the right place to put them. And then we could put links to those files in the *index.html* page. But going to get all those files and creating the references would be quite a bit of work ans it turns out there is an easier way.

Because Angular uses the Node Package Manager, or NPM, there is a node package for Bootstrap and we can use NPM to incorporate Bootstrap into our application. The way to do that is to issue the following command:

`npm install bootstrap jquery popper.js`

These are actually the three different frameworks that are needed for Bootstrap. We suggest you to do it from command line, do not use IDE. After that the actual files have been downloaded into our project, the are in the folder called *node_modules*. Then we reference these files in the head of our application. We don’t do that in *index.html* directly, rather it’s time now to look at another of the JSON files in root of our application - the *angular.json* file. One of the things this file does is it tells the Angular application where the different parts of the application exist that are needed for the application to run. For example there is a line that specifies which is the *index.html* page that is the source of every Angular page:

`"index": "src/index.html",`

One to the other things that is going to be relevant to us is the `"styles"` section which contains an array of files that make up these styles that need to be placed into the *head* section of our page:

```json
"styles": [
  "src/styles.css"
],
```

Now styles.css is currently an empty CSS file, but we can place every CSS code into this file and it will get applied automatically to every page of our site. We need to specify CSS and JavaScript files in the same way as we specify *styles.css* in *angular.json* file:

```json
"styles": [
  "src/styles.css",
  "node_modules/bootstrap/dist/css/bootstrap.min.css"
],
"scripts": [
  "node_modules/jquery/dist/jquery.min.js",
  "node_modules/popper.js/dist/umd/popper.min.js",
  "node_modules/bootstrap/dist/js/bootstrap.min.js"
]
```

That is everything we need for Bootstrap to work.

## 96 Exercise 1 - Referencing CSS and JavaScript files

- Copy the *calendar.css* into style.css
- Copy the font-awesome reference into the project

# Chapter 19 - Routing (30m)

## 97 Preparing the application

Earlier we created a static website with 3 different pages. When we click on each page the relevant information is loaded up. But the way that we achieved this was by showing or hiding different components based on the page selected. This approach is absolutely not production standard. We’re going to learn the correct way to do it. The main issue with what we’ve done is that every page, no matter which page we’ve got selected has the same URL. So it’s not possible to bookmark a page. In the Booking application that we’re going to be building we have the home page, which is the calendar page and there’s a couple of Admin pages and may be some others that we’re building as well, later on. And it makes sense for each of these to have separate URLs. Having separate URLs means that we can bookmark pages, we can create links to navigate to pages, and it is what users expect to see.

Let’s get started and we’ll create the outline structure of our application. The application is going to have a menu appear at the top of every page. So we’ll create menu as one component. And then we’ll want a component for each of the pages. For now we’ll create the calendar component, a rooms component, and a users component:

```tsx
ng g c menu
ng g c calendar
ng g c admin/rooms
ng g c admin/users
```

The next thing we’d like to do is create HTML for at least the *menu* component. We’d like to have the menu on every page, so we’ll put it at the top of the app component:

```html
<div class="container">
  <app-menu></app-menu>
</div>
```

## 98 Setting up basic routing

What we’d like to do is display the calendar component as the home screen and then when we click on one of the links in drop-down ‘Admin’ menu, display the relevant component. We need to set up what is called *routing* in Angular. *Routing* simply means assigning URL patterns to some kind of action such as showing a component. And we’re going to specify this in the app module. There is actually an alternative way to do this and we’ll show that at the end of this chapter. But we’ll go with the simplest option for now.

We setup *routing* in app.module.ts. Declare variable *routes* of type `Routes` before `@NgModule` decorator. The items in the *routes* array are going to be a list of JavaScript objects, so each one will need to be defined with an open and close curly bracket. The first parameter in the object is going to be the *path* parameter and we’re going to set the value to be the URL that we want to be navigating to. The 2nd parameter is what would we like to happen when we hit that URL. There’s a few different actions we can choose from, for now we want to just show a component, so we choose the component parameter and we set our component to be the component we’re interested in.

```tsx
const routes: Routes = [
  {path : 'admin/users', component : UsersComponent}
];
```

So we’ve defined our first route, we’ll add some others in a few minutes. Before we do that there are a couple more steps we need to do to make this work. The first is we need to tell Angular to use these routes to define the routing to make this variable actually mean something. The way that we do that is we need to tell Angular to use the Router module.

In the *imports* section in the app.module.ts we need to specify the **RouterModule** using the specified *routes* variable declared earlier: 

```tsx
imports: [
  BrowserModule,
  RouterModule.forRoot(**routes**)
],
```

We’ve now enabled routing in our application. We’ve told Angular that when the user goes to a specific URL, that is `'admin/users'`, then the *UsersComponent* should be shown. Actually that’s not quite accurate. What we’ve said is that the UsersComponent is going to be the active component. It actually doesn’t matter what the URL is, Angular is always going to load up and show the *index.html* page. This *index.html* page has a reference in it to *app-root*, so again, no matter what the URL is, the *AppComponent* is going to be loaded.

Within this *AppComponent* we’re showing the menu but here we also need to say, based on which is the route, we want to show a specific component, or we want to let the router decide what should be shown on this page. And we do that with a special tag called **router-outlet**. The router-outlet will go the router and ask Angular, based on the URL, what should router-outlet show here. So, for example, if we’ve navigated to `'admin/users'` then Angular will say for that particular URL we need to show the *UsersComponent*. So the *UsersComponent* will be placed where our router-outlet tag is.

```html
<div class="container">
  <app-menu></app-menu>
  <router-outlet></router-outlet>
</div>
```

## 99 Creating a 404 page

Let’s setup a couple more routes, one for the Rooms Admin page and one for the home page, our Calendar page. In our *app.module.ts* file we add more routes:

```tsx
const routes: Routes = [
  {path : 'admin/users', component : UsersComponent},
  {path : 'admin/rooms', component : RoomsComponent},
  {path : '', component : CalendarComponent}
];
```

When we build this application in Spring Boot we actually set the URLs for the rooms and the users page to be `/rooms` and `/users`. And those don’t match the routes that we’ve set up. We’re going to use `'admin/rooms'` and `'admin/users'`. We’ve done that for good reason. Later on we’re going to do some stuff with security, where we might say that the user must have special privileges to be able to access this separate menu. And it will be useful to have different URLs for those privileges. But as a result of that, if we click on one of these links, the `'/rooms'` URL didn’t exist and the application navigated back to the root of the application. If we look at the JS console, we’ll see an error there - “Cannot match any routes. URL Segment: 'rooms’”. That means that URL `rooms` didn’t exist. It didn’t match any of the rules in our defined routes.

What we really want is some kind of page-not-found functionality. Some kind of 404 page. To do that we’ll create a component that can be shown if we have a page-not-found. And then we can set up some routes to navigate to that component if an invalid route is encountered.

`ng g c PageNotFound`

To add this to our routing the first thing we’ll want to do is specify a route to show that component. Ant then we want one more route which is going to be if we find any URL other than the ones listed here, we want to redirect to this 404 URL. This is going to be a wildcard path, which is specified with two asterisk symbols - `**`. This wildcard path must be at the end of the list because the way that routing works in Angular is that it goes down this list to find the first path that matches.

```tsx
const routes: Routes = [
  {path : 'admin/users', component : UsersComponent},
  {path : 'admin/rooms', component : RoomsComponent},
  {path : '', component : CalendarComponent},
  {path : '404', component : PageNotFoundComponent},
  {path : '**', redirectTo: '/404'}
];
```

Now we have basic 404 functionality built in.

## 100 Making links work with routing

Things aren’t quite right just yet. When we’re clicking on the links in our menu, for example on the “Calendar” link, what happens is that the entire application is effectively being reloaded. A GET request is being sent to our server and the server is just responding with the full Angular application every time. Angular is meant to be a single-page-application. We should only ever do a single GET request to the server once when get the the application for the first time and then when we click on these links in the menu, the idea is that the content should change including the URL, but that should all be done by JavaScript, there should not be any further requests being made to the server.

To achieve this, in Angular, when you have a link, you never do an *href* in an anchor tag. Doing an *href* will cause a GET request to the server. We’re about to correct this. In the menu component we remove all of the *href*-s, except the one that is doing a drop-down (`href=”#”`), it is fine to leave. We’re going to create some code and bind that code to the click event for each of these anchor tags. Because we removed all *href*-s, when we hover over these different tags, they don’t look like they are clickable any more. We need to change styling of our anchor tags to make them look clickable:

```css
a {
  cursor: pointer;
}
```

Then we’ll create some click events in *menu.component.html* :

```html
<a class="dropdown-item" (click)="navigateToRoomsAdmin()" >Rooms</a>
<a class="dropdown-item" (click)="navigateToUsersAdmin()" >Users</a>
```

and methods in *menu.component.ts*:

```tsx
constructor(private router: Router) { }

navigateToHome(): void {
  this.router.navigate(['']);
}

navigateToRoomsAdmin(): void {
  this.router.navigate(['admin', 'rooms']);
}

navigateToUsersAdmin(): void {
  this.router.navigate(['admin', 'users']);
}
```

## 101 Using a separate routing file (5m)

There is a warning in our IDE on this *navigate* command: `this.router.navigate(['admin', 'rooms']);` - “promise returned from the navigate is ignored”. We’re going to learn what *promise* is in the next module. This is one warning that can safely be ignored. TS Lint is saying that we may have made an error. This is one instance where when we use *navigate* we’ve done everything just right, there is no error here at all and we can safely ignore it.

The structure we’ve built up then contains the routing rules for the app module. And what we’ve built is absolutely fine, it’s production standard, but it’s possible that you’ll have a large number of routes for your application and you might find it easier to put these into a separate file. Just so that the app module file doesn’t become overly long. From a performance point of view there’s no reason to do that, it’s simply you choice to make your code slightly easier to maintain. 

However if you let Angular create the routing structure for you when you generate a new project (we get that option when we create an Angular project) then it does actually create the routing rules in a separate file for you. Actually the correct terminology is it creates them in a separate module, but we don’t really want to get into the detail of what a module is just yet.

We’re not going to change the structure of our application. If you want to view the alternative structure that is created for routing in a separate module you can choose that option when creating a new Angular project. It will contain an additional class *AppRoutingModule* where you can add routing rules for your project.

# Chapter 20 - Models and Views (53m)

# Chapter 21 - Using Observables for data (8m)

## 110 Why we should use observables with data

In the last chapter we built a local data service which stores information about our rooms and users. Later on we’re going to be creating the ability to swap this version out with a new version that will connect to a REST backend. Before we’re going any further we want to change the way that the data service works. The reason for this is as follows. When we later on connect to the backend, we’ll getting the data using REST. The idea is that we’re going to replicate the local data service with a 2nd version that uses REST to get the data. And that will have some significant differences to what we’ve done so far.

The REST version of our data service is not going to be able to have the publicly available arrays of data. If we want to be able to get a list of rooms, we’re going to be making a call to some backend server, so the REST data version is going to need some kind of getRooms method.

The first thing we’ll do is make these 2 arrays (rooms and users) `private` and create getter methods to return them: `getRooms(): Array<Room>` and `getUsers(): Array<User>`. Then we need to change some code that uses the arrays.

That is the first step. But there is still a little bit to do. When we call the *getRooms()* on the local data service the method can return the data we need instantly. However when we call the equivalent method in the REST version of this class, that we’ll be creating later on, the method is going to have to connect to some backend server to get the data, so that means it’s not going to be instant. And of course the backend might not respond. We’ll learn properly how to deal with that case, when the server doesn’t respond, in the next module. But for now it’s enough to know that REST calls in Angular work by using the Observer Design Pattern.

When we come to work with REST we’ll issue the call the get the list of rooms and rather than receiving back an array of rooms we’ll get something we can subscribe to, and the data, the actual rooms, will be filled in or provided later on. When the data arrives, it’s an event, that we can respond to. So the methods that we’ve just created that can be return an array of rooms or an array of users, when we create the REST version, they’re not going to be returning an array, but rather something we can subscribe to, which will later on give us the array.

So before we start creating lots more methods in this local data service, we think it’s worth switching these two methods, the *getRooms()* and *getUsers()* , to this alternative design pattern. That is to make our local data service return something we can subscribe to. We are going to provide a mark implementation for the REST data service. We’re going to pretend that it might take some time to get the data that we need and that we’ll be providing that data after a few seconds. Actually that’s not quite what we’ll do, we weren’t supposed to delaying, but the idea is certainly that we’re mocking a REST call.

So the method signature is not going to return an array of rooms but it’s going to return an observable of an array of rooms.

in data.service.ts:

```tsx
private rooms: Array<Room> = new Array<Room>();
private users: Array<User> = new Array<User>();

getRooms(): Observable<Array<Room>> {
  return of(this.rooms);
}

getUsers(): Observable<Array<User>> {
  return of(this.users);
}
```

in rooms.component.ts:

```tsx
ngOnInit(): void {
  this.dataService.getRooms().subscribe(
    (next) => {
      this.rooms = next;
    }
  )
  // ...
}
```

in users.component.ts:

```tsx
ngOnInit(): void {
  this.dataService.getUsers().subscribe(
    (next) => {
      this.users = next;
    }
  )
  // ...
}
```

# Chapter 22 - Template forms (39m)

## 111 How template driven forms work (1m)

So far everything we have done with Angular has been *read-only*. In this chapter we’re going to start learning how to create forms to allow users to input data. There are two different ways to do forms in Angular called *template driven* and *reactive*. Template driven is easier of the two, so we’ll focus on this method in this chapter and we’ll look at the alternative reactive forms in a couple of chapters time.

The concept behind how template drive forms work is relatively straightforward. We’ll use HTML elements, such as *input* and in the backing TypeScript file we’ll create variables that we will bind to these elements. So as the value in the *input* changes, the variable in the backing file will be automatically updated. We don’t actually submit a form to a server, but instead we’ll have the equivalent of a submit button which will be bound to a method in the TypeScript file that can read these variables to get the form’s values

## 112 Setting up navigation to show a form (9m)

Let’s create a form for User editing. In Spring the form loads as a separate page, but in the Angular version it would be nicer if we made the edit form appear replacing the view for a particular user details.

The first step is to create a component for editing users: `ng g c admin/users/UserEdit`

We will make the form appear in a production standard way. Right now we’re using the URL with a query parameter (`?id=2`) to determine which user to show. It would be sensible if we upgrade this to say as well as the query parameter `id` we’ll add in a second parameter which we’ll call *action* and we can set it equal to either *view* or *edit* if we want to edit user (`?id=1&action=edit`). We’re going to upgrade the application to work with 2 parameters, not just one.

user-edit.component.ts:

```tsx
export class UserEditComponent implements OnInit {
  @Input()
  user: User;

  ngOnInit(): void {
  }
}
```

user-edit.component.html:

```html
<p>user-edit works! The user we're editing is {{ user.name }}</p>
```

users.component.ts:

```tsx
action: string;

constructor(private dataService: DataService,
              private route: ActivatedRoute,
              private router: Router) { }

ngOnInit(): void {
  // ..
  this.route.queryParams.subscribe(
    (params) => {
      const idString = params['id'];
      const action = params['action'];
      if (idString) {
        const idNumber = +idString;
        this.selectedUser = this.users.find(user => user.id === idNumber);
        if (action) {
          this.action = action;
        }
      }
    }
  )
}

setSelectedUser(userId: number): void {
  this.router.navigate(['admin', 'users'], 
												{queryParams: {id: userId, action: 'view'}});
}
```

users.component.html:

```html
<div class="col-6">
  <app-user-detail *ngIf="action === 'view'" [user]="selectedUser">
	</app-user-detail>
  <app-user-edit *ngIf="action === 'edit'" [user]="selectedUser">
	</app-user-edit>
</div>
```

user-detail.component.ts:

```tsx
constructor(private router: Router) { }

editUser(): void {
  this.router.navigate(['admin', 'users'], 
									{queryParams: {id: this.user.id, action: 'edit'}});
}
```

in user-detail.component.html:

```html
<a class="btn btn-small btn-warning"
   (click)="editUser()"
>edit</a>
```

## 113 Creating a form (5m)

in user-editcomponent.ts:

```tsx
message: string;
```

user-editcomponent.html:

```html
<h1>{{ user.id == null ? 'Add' : 'Edit'}} User</h1>
<div class="bg-warning p-5" *ngIf="message">{{ message }}</div>
<form action="some-url-goes-here" method="post">
<div class="form-group">
  <label for="name">Name</label>
  <input type="text" class="form-control" id="name" 
				 placeholder="user name">
  <div class="alert alert-danger">error message placeholder</div>
</div>
<div class="form-group" *ngIf="user.id == null">
  <label for="password">Password</label>
  <input type="password" class="form-control" id="password" 
				 placeholder="password">
  <div class="alert alert-danger">error message placeholder</div>
</div>
<button type="submit" class="btn btn-primary">Save</button>
</form>
```

## 114 Binding a form (10m)

The first thing to note here is that unlike if we were doing this in JSP or Thymeleaf we don’t have to worry about exactly replicating an object that we’re going to bind this form to. What we mean by that is that we don’t have a reference on the form to the ID and that’s absolutely find. If we were working in JSP or Thymeleaf we would probably have to put in a hidden ID field somewhere in the form so that when the form is sent back to the server there is enough data there for the server to be able to interpret it. This form is going to be interpreted in JavaScript running on the browser so the ID can be held in memory, we don’t need it on the form. So forms can be a lot more straightforward and simple in Angular. This form has 2 fields although one of them can be invisible.

The next thing to note we’re not sending this form to a server. Instead we’re going to create a method, that will run when the form is processed, when we click on the Save button.

We need to change some of the forms attributes. Right now the form has an *action* attribute with a placeholder for URL. We’ll remove that action. Instead we’ll run some code when the Save button is clicked.

115 Saving the form data (5m)

116 Finishing the form (9m)