# Module 4 - Connecting to a REST backend

# Chapter 27 - Setting up environments (25m)

## 139 Creating environment variables (7m)

What we’ve built so far is a functioning Angular site, but it works in a completely detached way. Before we start connecting it to the backend however, this is a good time to introduce the idea of environments. If you’ve worked with Spring, you can create different application dot properties files so that you can have one profile for development and a different profile for production, and so on. That’s exactly the same idea as what we’re going to do in Angular.

We are going to have some differences between our development and production versions of the Angular site. The differences are quite small. It’ll simply be that we’ll be connecting to a different REST URL for the backend system. While we are in development we’ll be connecting to `http://localhost:8080`. When we are in production and we’ve deployed our application to a server somewhere on the Internet, we’ll be connecting to some kind of URL that is accessible by the Internet (something like `https://someServer.com`) and hopefully we’ll use `https` rather than `http` for that connection. To achieve this difference we’ll be using the same underlying code, but we’ll have the URL that we want to connect to as a variable or property of some class.

Angular gives us a way to have these different properties based on different profiles and the place to define them is in the folder called *environments*. In Angular under the *src* folder you’ll see there is a folder called environments. We’ll be defining properties that we’re going to use during development in the file called `environment.ts`, and properties that we’re going to use when we run the application on a production server in the file `environment.prod.ts`. We can add in our own key-value pairs in the variable called *environment*.

### environment.ts

```tsx
export const environment = {
  production: false,
  restUrl: 'http://localhost:8080'
};
```

### environment.prod.ts

```tsx
export const environment = {
  production: true,
  restUrl: 'https://someServer.com'
};
```

And do view how to use it we’ll go to data service:

### in data.service.ts

```tsx
// IMPORTANT: You should never import from environment.prod !!!
import {environment} from "../environments/environment";
//...
constructor() {
  console.log('environment.restUrl=', environment.restUrl);
	//...
}
```

By default when you run `ng serve` command it will run in our development profile. To run the application with the production profile we need to give *ng serve* a command line argument. We’ll be going to these details a little later (`ng serv --prod`).

## 140 How environments work (2m)

It’s worth understanding how this works because when we know that, it allows us to do much more with environments  and it turns out to be a really useful feature for us and in fact for most projects.

We need to look at one of Angular’s configuration files which is the one called *angular.json*. The idea is this - when we run Angular with no command line parameters, it runs with the default configuration, which is what we’re describing as the development environment. What this simply means is that all the normal files are used. So in the data service because we imported *environment* from `environment.ts` file, the variables in that file were used. So `environment.ts` is the normal version of the environment file or the default version, and therefore whenever we don’t define any runtime arguments that’s the file that gets used.

When we tell Angular to run in production mode, however, the *angular.json* file says that in this configuration we need to do some swaps. We’re going to swap out the *environment.ts* file and will replace it with *environment.prod.ts* file (section “production”, “fileReplacements” in angular.json).

## 141 Creating our own environments (11m)

So out of the box Angular has 2 environments called *Production* and *Default*. We’re going to create our own third configuration. When we’ve finished, we’re going to be able to run our frontend in three different ways. We’ll have a *Production* environment, which will contain all the configuration and code we need to connect via REST to a server on the Internet, we’ll also have a *Development* environment, which will contain all the configuration and code we need to a server running on the *localhost* doman, and we’re going to create a 3rd environment which we’re going to call *Local* . This will be the system running exactly as it is now. Not using REST, but instead using the local data service built into the Angular application (standalone version that doesn’t need a backend and is running with a dummy data).

Here is the plan. Right now we have the DataService class. We’re going to create another version of this class, so that we’re going to have the choice of which version of our class to use. One version will use REST calls and over version will use local data. In Development mode we’ll use the REST version and use the Default environment to find out the URL to connect to. When we run in Production mode we’ll again use the REST version but we’ll get the URL from the Production environment file. And when we run in Local mode we’ll use the local version of the data file.

We’ll make the Development mode (RestService, localhost:8080) the Default. We’ll create alternatives for Producion (RestService, someServer.com) and Local(LocalService, no url) mode.

We saw that in angular.json we can replace files, we’ve seen the environment file for different modes. And we can do exactly the same thing with our own classes. We can create the 2nd version of our data service and use data service by default, but in other mode swap that out and use a different version.

Now we’re importing data service from *data.service.ts* file.  So this data.service.ts is the default class. That means when we’ve finished, it’s the development version, or in other words our existing *DataService* class is going to turn out to be the one that’s going to connect via REST.

We’re going to create the 2nd version now, and it’s going to be the local version. Normally we would create a service using the CLI. However in this instance, because we’re going to replicate this existing data service class, we’re not going to use the CLI to do it. We’re going to do it manually. The reason is that we’re going to reuse the class name in this second version. And the format of this file name isn’t going to match what we’ve seen. We’re going to create a file called *data.service.local.ts*. We create a *data.service.local.ts* file in the *app* folder. We copy and paste the entire *data.service.ts* file into this local version. It is really important because we’re going to program against *data.service.ts* file, but occasionally swapping it for *data.service.local.ts* file. These two files that have the same class in them need to have exactly the same public method signatures. Otherwise we’re going to run into problems later on. There is no checking, it is up to us to make sure they match at all times. From now on, if we make any of the method signatures in one file, we’ll need to also make the same changes in the other file.

We leave data.service.local.st intact, but we start making changes in data.service.ts which is now going to become the REST version.

Now we cut out existing  code in data.service.ts. We remove private variables. And all of the methods that return something, for now they just return null. And we remove code from the constructor.

DP: To make this class compile I had to set "compilerOptions": { "strict": **false**} in tsconfig.json.

### in angular.json

```tsx
"configurations": {
  **"local"**: {
    **"fileReplacements": [
      {
        "replace": "src/app/data.service.ts",
        "with": "src/app/data.service.local.ts"
      }
    ]**
  },
  //...
}

"serve": {
  "builder": "@angular-devkit/build-angular:dev-server",
  "configurations": {
    "production": {
      "browserTarget": "BookingClient:build:production"
    },
    "development": {
      "browserTarget": "BookingClient:build:development"
    },
    **"local": {
      "browserTarget": "BookingClient:build:local"
    }**
  },
	//...
}
```

## 142 Selecting an environment to serve (4m)

In order to select an environment at runtime we’re going to use runtime argument 

DP: I had to comment out 2 lines in `.browserslistrc` file:

```tsx
# last 2 Safari major versions
# last 2 iOS major versions
```

To run with local configuration: `ng serve -c local`.

# Chapter 28 - Creating the REST API (28m)

## 143 Creating a rest method (7m)

The focus is going to be on what you need to do to ensure the application works with Angular.

The one thing we need to know at this point is that Angular uses JSON as the data format for REST and that’s great because it is the default for Spring as well, as long as your methods return some kind of object. So the return type for the methods that we create is going to be important. In general, we won’t to create methods that return a string. We’ll always want to return either an instance of a class or some kind of collection object.

We should create a new package for our REST controllers called *rest*. And we create class RestRoomController:

```java
@RestController
@RequestMapping("/api/rooms")
public class RestRoomController {
    private final RoomRepository roomRepository;

    public RestRoomController(RoomRepository roomRepository) {
        this.roomRepository = roomRepository;
    }

    @GetMapping
    public List<Room> getAllRooms() {
        return roomRepository.findAll();
    }
}
```

## 144 Testing rest method (3m)

We can view the response of this method by any browser at this URL:

 `http://localhost:8080/api/rooms`

We also can use *curl* or *httpie* utilities.

```bash
curl http://localhost:8080/api/rooms     # get response
curl -I http://localhost:8080/api/rooms  # view headers
```

## 145 Exercise 1 - creating rest methods (2m)

Create the Rooms REST API:

| HTTP VERB | URL | Action |
| --- | --- | --- |
| GET | /api/rooms | Get all rooms |
| GET | /api/rooms/123 | Get the room with id=123 |
| POST | /api/rooms | Add a room |
| PUT | /api/rooms | Update a room |

Create the Users REST API (don’t worry about error handling).

## 146 Exercise 1 - solution walkthrough (8M)

## 147 Adjusting the User object

Now the user’s password is not encoded in the database. We’ll create a 2nd version of the User object which doesn’t contain the password.

```java
public class AngularUser {
    private Long id;
    private String name;
		// ...
    public AngularUser(User user) {
        this.id = user.getId();
        this.name = user.getName();
    }
    public User asUser() {
        User newUser = new User();
        newUser.setId(this.id);
        newUser.setName(this.name);
        newUser.setPassword("");
        return newUser;
    }
}
```

# Chapter 29 - Connecting to a REST endpoint (41m)

## 148 Initiating the call to a REST endpoint (9m)

In this chapter we’re going to learn how to call a REST resource from Angular. The process of communicating between Angular and a backend using REST should be easy, but as we’ll see there are some rather annoying complications to get this to work. So we will have a bit of work to do to make our first successful REST call. We could just show you the full solution to how to do this, but instead, we’re going to be working through it step by step over the next few chapters, so that we encounter all the errors and problems together that could occur, and then hopefully by the end of this process, you’ll not only have a good idea of exactly how this works, but if you encounter similar errors in your own projects, you’ll know exactly how to solve them.

The starting point is that we’ll need to import a new module into our project which is called the HttpClientModule. So we’ll start in app.module.ts, and we have a list of modules here in the “import” section. We need to add in another one, which is HttpClientModule. 

### in app.module.ts

```tsx
imports: [
  BrowserModule,
  FormsModule,
  ReactiveFormsModule,
  **HttpClientModule,**
  RouterModule.forRoot(routes)
],
```

And having this module in app.module allows us then to use an object called HttpClient in our project’s classes. And we’ll get that object using dependency injection.

Before we go any further, let’s make sure, let’s make sure we’re running with the development environment.

In the data service’s constructor, we want to use dependency injection to get an instance of HttpClient class.

### in data.service.ts

```tsx
constructor(**private http: HttpClient**) {
  console.log('data.service.ts: environment.restUrl=', 
							 environment.restUrl);
}
```

Now we’d like to create a method to get a single user object. We’re going to create an extra method in here. We will be deleting this later on, we’re not going to copy this into the other version of the data service class.

### in data.service.ts

```tsx
getUser(id: number) : Observable<User> {
  console.log('data.service.ts: getUser with id=', id)
  return this.http.get<User>(
														environment.restUrl + '/api/users/' + id);
}
```

### in calendar.component.ts

```tsx
ngOnInit(): void {
  this.dataService.getUser(13).subscribe(
    user => {
      console.log('in calendar: user:', user);
      console.log('in calendar: typeof user:', typeof user);
    }
  );
	// ...
}
```

Actually this isn’t going to work, because there are going to be some complications.

## 149 Cross Origin Resource Sharing (CORS) (5m)

The error is: 

Access to XMLHttpRequest at 'http://localhost:8080/api/users/13' from origin 'http://localhost:4200' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.

CORS is a security policy which is implemented in browsers to protect users against what are known as hijacking attempts, and the idea behind CORS is this. A typical web page might have some JS code blocks in it. Thee JS code blocks will implement CORS to servers after the page has loaded. If you are familiar with AJAX, that would be a good example. The page loads first, and then, a script within the page goes off to some server to get some data to display on the page. In fact, that’s exactly what we are trying to do. The browser already has the page. It’s now just making a request to get some data. If that JS request is made to the same server that the page originally came from, my browser will be quite happy with that. The problem can occur if the data is coming from somewhere else. If the JS is initiating a call to a different server to get some data from that, then the browser will block that request by default. The risk is that the server we know about, the one that gave us the web page in the first place, could have provided the JS in our browser with some sensitive information, and by default, browsers don’t allow us to send any information to other servers without explicit permission to allow them to do so.

When we want to communicate with a different server, what we’re doing is called a cross-origin-request, meaning that we’ve crossed from one domain to another. And to allow that to happen, we’ll need to enable cross-origin-resource sharing or CORS for short.

Now in our application the web page comes from localhost:4200, and it’s trying to get data from localhost:8080. That is a different origin. The port number is enough for these to be considered different servers. And in fact, of course, as far as our computers concerned, they are different servers. They are different processes, each running their own server, just on a different port. When we go to deploy these to a live production environment, well, our backend and the Angular application are going to have different URLs, so these are also going to be different servers. The same issue is going to occur in production. So although the code we’ve written is correct, our browser is blocking the request. It actually still fires off the request to he endpoint, it reaches the server, but it blocks the response we get back from the server. 

This is a security policy implemented in browsers and we need to tell the browser that this response it got from localhost:8080 is a permitted response. We want to tell the browser that we’re going to allow responses from that server in this application. And that is some configuration we actually do in the server. So, we’re going to set p some configuration in Spring.

## 150 Configuring CORS in Spring (6m)

This is primarily an Angular course, not a Spring course. So we’re focusing here on the configuration we need to do for Spring to make it work with Angular, and we’re not going to go into a huge amount of detail about all the different ways we can configure Spring and what all the different options are. We just want to show you enough to make it work with Angular. We’re actually going to put an extra header into every REST response that says that this CORS option is going to be permitted. So we’re going to create a configuration class in Spring and then use that configuration class to allow these cross option requests.

We’ll create a new package called *config* and a new class *CorsConfig*.

### in *CorsConfig.java*

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedMethods("GET", "POST", "PUT")
                .allowedOrigins("http://localhost:4200");
        // Need to change the URL for the production URL 
				// when we deploy
    }
}
```

## 151 Manipulating the REST return data type (7m)

The object type we get back is not a user object. As we can see it’s an object of type *object*. It is a JavaScript object and it has key-value pairs containing the values. It’s not even easy to convert this to an object of type *User*. Most of the time this isn’t a problem. But it will be a problem if our User class has methods that we want to be able to call. If we want to be able to make it a genuine User object, we can’t just convert it or cast to it. What we actually need to do is create an instance of the *User* class from the data that we get back. The way we’re going to do that is by creating a helper method in the *User* class itself.

### in User.ts

```tsx
static fromHttp(user: User): User {
  return new User(user.id, user.name);
}
```

### in calendar.component.ts

```tsx
ngOnInit(): void {
  this.dataService.getUser(13).subscribe(
    user => {
      console.log('in calendar ngOnInit(): user:', user);
      console.log('in calendar ngOnInit(): typeof user:', 
																							typeof user);
      const tsUser: User = User.fromHttp(user);
      console.log('in calendar ngOnInit(): tsUser:', tsUser);
      console.log('in calendar ngOnInit(): tsUser.isNotNew():', 
																							tsUser.isNotNew());
    }
  );
	// ...
}
```

## 152 Pre-processing the REST return data (5m)

So we’ve seen that we can manipulate the returned data type, but actually it would be nicer if we can do this as part of the subscribe process to do some preprocessing on the data. We can do some preprocessing in the data service on the *http.get* method before subscribe runs.

### in data.service.ts

```tsx
getUser(id: number) : Observable<User> {
  return this.http.get<User>(environment.restUrl + '/api/users/' + id)
    .pipe(
      map(data => User.fromHttp(data))
    );
}
```

You understand now the principles of how to make REST calls work. We’ve seen two complications so far. Browsers block calls by default so we have to enable it by configurint our server correctly, and REST requests receive back JavaScript objects that we’ll normally want to convert into real class instances by applying some kind of preprocessing using *pipe(map)* construct.

This code in the calendar component is for illustration purposes and will be removed later on.

## 153 Optional calling a REST endpoint exercise (1m)

Implement *getUsers()* and *getRooms()* methods.

## 154 Pre-processing complex data (8m)

### in Rooms.ts

```tsx
export class Room {
	// ...
  static fromHttp(room: Room) : Room {
    let newRoom = new Room(room.name, room.location, room.id);
    for (const lcData of room.capacities) {
      newRoom.addLayoutCapacity(LayoutCapacity.fromHttp(lcData));
    }
    return newRoom;
  }
}
export class LayoutCapacity {
	// ...
  static fromHttp(lcData: LayoutCapacity) : LayoutCapacity {
    return new LayoutCapacity(Layout[lcData.layout], lcData.capacity);
  }
}
```

### in data.service.ts

```tsx
getRooms(): Observable<Array<Room>> {
  return this.http.get<Array<Room>>(
													environment.restUrl + '/api/rooms')
    .pipe(
      map(data => {
        const rooms = new Array<Room>();
        for (const roomData of data) {
          rooms.push(Room.fromHttp(roomData));
        }
        return rooms;
      })
    );
}

getUsers(): Observable<Array<User>> {
  return this.http.get<Array<User>>(
													environment.restUrl + '/api/users')
    .pipe(
      map(data => {
        const users = new Array<User>();
        for (const userData of data) {
          users.push(User.fromHttp(userData));
        }
        return users;
      })
    );
}
```

# Chapter 30 - Dealing with slow and unavailable connections (26m)

## 155 Dealing with slow REST responses (5m)

In this chapter we’re going to understand what would happen if our backend server wasn’t available, so the REST call doesn’t work. Before we get to that we’ll also see what happens if it does work but there’s a delay between the initiation of the REST call and the response being received back from the server. May be the network is slow, or the server has a lot of data to send back, so it’s a response which takes at least a second or two.

Before we get started we want to remove the test data we wrote in the calendar component in the last chapter and also the extra method *getUser(id)*.

To simulate slow response we’re going to put a delay in the backend.

### in RestRoomController.java

```java
@RestController
@RequestMapping("/api/rooms")
public class RestRoomController {
    //...
    @GetMapping
    public List<Room> getAllRooms() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            throw new RuntimeException("Delay interrupted", e);
        }
        return roomRepository.findAll();
    }
}
```

### in rooms.component.ts

```tsx
**loadingData = true;**

ngOnInit(): void {
  this.dataService.getRooms().subscribe(
    (next) => {
      this.rooms = next;
      **this.loadingData = false;**
    }
  )
  //...
}
```

### in rooms.component.html

```html
<div *ngIf="loadingData"
>Please wait... getting the list of rooms</div>
```

## 156 Catching REST errors (3m)

Let’s see what happens when an error occurs. The easiest way to simulate this is if we stop our backend server from running. If we do that then our page is stuck with “Please wait...” message and we receive error in console.

It’s important whenever we’re dealing with REST calls that we always need to think about what we’re going to do when an error occurs. When an error occurs we could use additional parameter *error* in the subscribe.

### in rooms.component.ts

```tsx
**message = 'Please wait... getting the list of rooms';**

ngOnInit(): void {
  this.dataService.getRooms().subscribe(
    (next) => {
      this.rooms = next;
      this.loadingData = false;
    },
    **(error) => {**
			// try to replace this error.message with a more 
			// meaningful message to the user
      **this.message = 'Sorry - something went wrong, please try again.'
        + error.message;**  
      console.log('ngOnInit: error:', error);
    }
  )
}
```

### in rooms.component.html

```html
<div *ngIf="loadingData">{{ message }}</div>
```

## 157 Investigating REST errors (5m)

If we look at the console, we can have a look at an error object. We would expect to see an error somewhere in the 400 or 500 ranges but we don’t see that here. What we see is a *status* value and that would be the field that contains the error code, which we would expect to be something like 404 if the page wasn’t found for example. And what we get here is a zero:

```json
{
    "headers": {
        "normalizedNames": {},
        "lazyUpdate": null,
        "headers": {}
    },
    **"status": 0,**
    "statusText": "Unknown Error",
    "url": "http://localhost:8080/api/rooms",
    "ok": false,
    "name": "HttpErrorResponse",
    "message": "Http failure response for http://localhost:8080/api/rooms: 0 Unknown Error",
    "error": {
        "isTrusted": true
    }
}
```

To view what would normally happen we could change the method to return an invalid status.

### in RestRoomController.java

```java
@GetMapping
    public List<Room> getAllRooms(HttpServletResponse response) {
        response.setStatus(402);
        return null;
    }
```

Then it we refresh the call to get all rooms we receive status code 402 in the error object. So most of the time we would expect to be able to query the errors status value to find out what went wrong. So it would be possible to write code that says “if status equals 402 then display this message, if the status is 401 then display another message”.

If there is a genuine error we’ll get a status code we can do something with it. But if the error is that the server didn’t respond then the status code comes back as being a zero. The reason for this is a little bit complicated. It’s a feature of the browser. It’s fine to investigate the value of error.status but a status of zero actually means that the server didn’t respond. Any other status code would have the right value and that would allow us to do something with it.

This is the example of how you can use error status:

### in rooms.components.ts

```tsx
ngOnInit(): void {
  this.dataService.getRooms().subscribe(
    (next) => {
      this.rooms = next;
      this.loadingData = false;
    },
    (error) => {
      if (error.status === 402) {
        this.message = 'Sorry - you need to pay to use this application';
      } else {
        this.message = 'Sorry - something went wrong, please try again.';
      }
    }
  )
}
```

## 158 Retrying when an error occurs (6m)

If an error 402 occurs that’s effectively a permanent problem. We just want to display a message to the user and stop. But if something else goes wrong, it might be a temporary fault, it might be a network problem, may be the server is overloaded, but try again would work. We are displaying a message “Please try again...” but it would be nice if we could get our code to automatically try again.

When we learned about the subscribe method using our Observer design pattern, as soon as the error occurs, the subscription is lost. The object is automatically unsubscribed and what that means is that if data comes through after an error, we’ll never see it. Actually the way that a REST would work, that can never happen, we won’t ever get data come through after an error. But the idea is then that if we want to do an automatic re-try, we’ll need to reissue the command to try and get the rooms. Let’s see how it might look.

First we refactor out the code to a separate method *loadData()*. Then we introduce class level variable to count attempts to load data.

### in rooms.components.ts

```tsx
ngOnInit(): void {
  this.loadData();
  //...
}

loadData(): void {
  this.dataService.getRooms().subscribe(
    (next) => {
      this.rooms = next;
      this.loadingData = false;
    },
    (error) => {
      if (error.status === 402) {
        this.message = 'Sorry - you need to pay to use this application';
      } else {
        this.reloadAttempts++;
        if (this.reloadAttempts < 10) {
          this.message = 'Sorry - something went wrong, please trying again.... please wait';
          this.loadData();
        } else {
          this.message = 'Sorry - something went wrong, please contact support';
        }
      }
    }
  )
}
```

## 159 Surviving a page refresh (4m)

Before we leave this chapter, we want to point out that there’s a small problem with what we’ve built so far and we’d like to fix it before we move on.

If we refresh the rooms page, this will work absolutely fine. But let’s imagine we’re viewing one of these rooms, for example room number 1. The url contains id of 1 and action of “view”. If we do a browser refresh on this page, then we get an error in our console. The list of rooms does appear, but the details of a selected room does not.

The reason is that when we refresh the browser, the application is loading from scratch. All the data or state in the application is completely lost. So when the code in ngOnInit runs, all of the class level variables such as rooms, selectedRoom are going to be empty. Every time we refresh the page *rooms* will be emtpy. When we’re going to a specific URL, which is to view a room with a specific ID, then we don’t have initialized array of rooms yet to find a selected room. Our server, because there’s a slight delay, hasn’t responded with a list of rooms. We’re trying to run some code against an uninitialized variable *rooms*.

The way that we’ll get round that is we don’t want the whole code block dealing with the query parameters to run unless our data has been loaded. We’re moving this code into a separate method *processUrlParams()* and we’re going to call it after we have successfully loaded the data within the method *loadData()*.

### in rooms.components.ts

```tsx
**ngOnInit(): void {
  this.loadData();
}**

loadData(): void {
  this.dataService.getRooms().subscribe(
    (next) => {
      this.rooms = next;
      this.loadingData = false;
      **this.processUrlParams();**
    },
    (error) => {
      //...
    }
  )
}

**private processUrlParams()** {
  this.route.queryParams.subscribe(
    (params) => {
      const idAsString = params['id'];
      if (idAsString) {
        const idAsNumber = +idAsString;
        // @ts-ignore
        this.selectedRoom = this.rooms
																.find(room => room.id === idAsNumber);
      }
      this.action = params['action'];
      if (this.action === 'add') {
        this.selectedRoom = new Room();
        this.action = 'edit';
        this.formResetService.resetRoomFormEvent
															.emit(this.selectedRoom);
      }
    }
  )
}
```

## 160 Exercise 2 - Dealing with slow and unavailable connections (1m)

Everything we’ve done so far we’ve done in the Rooms component. We’d like you to replicate this in the Users component. So we want to be able to deal with if the server doesn’t respond, if there’s an error, and if the server responds with a slow response, having a nice user experience in Angular.

For this exercise when an error occurs, just display a nice message to the user, we don’t think you should replicate the code that does the retries or that looks at what the error actually is and displays a message depending on the error. Let’s just display a generic message saying, “I’m sorry, an error has occurred. You’ll need to contact support.”

We need to deal with:

- Slow responses
- Errors
- Browser refresh

## 161 Exercise 2 - Solution walkthrough (3m)

### in users.components.ts

```tsx
**loadingData:** boolean = true;
**message** = 'Please wait... getting the list of users';
// ...
ngOnInit(): void {
  this.dataService.getUsers().subscribe(
    (next) => {
      this.users = next;
      **this.loadingData = false;**
      **this.processUrlParams();**
    },
    (error) => {
      this.message = 'Sorry, an error has occurred'
    }
  )
}

**private processUrlParams() {**
  this.route.queryParams.subscribe(
    (params) => {
      const idString = params['id'];
      if (idString) {
        const idNumber = +idString;
        // @ts-ignore
        this.selectedUser = this.users
																.find(user => user.id === idNumber);
      }
      this.action = params['action'];
    }
  )
}
```

### in users.components.html

```html
<div *ngIf="loadingData">{{ message }}</div>

```