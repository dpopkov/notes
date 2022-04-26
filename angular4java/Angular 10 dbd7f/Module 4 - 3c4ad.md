# Module 4 - Connecting to a REST backend (part2)

# Chapter 31 - POSTing and PUTing data (32m)

## 162 Sending data via REST (10m)

In this chapter we’re going to expand our REST calls to allow us to add and edit users and routes. We’re going to start with the users component and what we should be able to do is really just build up the relevant methods in the REST data service. We’ll start with updating a user. So we’ll be able to click on edit, change the user’s name and then click on save and send that updated data to the server.

So we’ll go to the data service, that’ll be the REST version of our data service and let’s find a method to update a user. To do an update we use the PUT verb.

### in data.service.ts

```tsx
updateUser(toUpdate: User): Observable<User> {
  return this.http.put<User>(environment.restUrl + '/api/users', 
															toUpdate)
    .pipe(map(data =>  User.fromHttp(data)));
}
```

If we run this and change the name of the user, then it doesn’t look that the change has worked. But if we refresh the page we’ll see that the change did indeed work and our server has sent back the new name for our user. So after doing an edit, we’ll need to refresh the list of users. We could potentially update the users list that is held in memory by the parent users component, but we think it is safer to go to the server and get the updated list of users. Let’s just not rely on assuming that the list has indeed been updated.

We already have a method in the data service to get the list of users, so we should be able to reuse that list. Actually in the users component we’re calling that in ngOnInit. We think it would be sensible if we re-run the ngOnInit method. Well, running ngOnInit many times isn’t really sensible so what we should actually do is move this code out of ngOnInit. Let’s put it in a method called *loadData*.

### in users.component.ts

```tsx
ngOnInit(): void {
  this.loadData();
}

loadData() {
  this.dataService.getUsers().subscribe(
    (next) => {
      this.users = next;
      this.loadingData = false;
      this.processUrlParams();
    },
    (error) => {
      this.message = 
					'Sorry, an error has occurred - please contact support.'
    }
  )
}
```

Now we can re-run the *loadData* method whenever we like to refresh the user list.

But there is a small problem, which is edit functionality, that we’re carrying out, is not in the users component. It’s in a child of the component called the UserEditComponent. There is a way that the child component can tell the parent component it needs to do something. The solution to how get over this problem is with events. What we’ll need to do is create an event in the child component that says some data has been changed, we’ll bind to that event for the parent component and when that event happens we’ll re-run the *loadData* method.

We’re going to start in the user edit component and in here we want to have an event *dataChangedEvent* and we’ll want to fire that event on the saving method.

### in user-edit.component.ts

```tsx
**@Output()
dataChangedEvent = new EventEmitter();**

onSubmit(): void {
  if (this.formUser.id == null) {
    this.dataService.addUser(this.formUser, this.password).subscribe(
      (user) => {
        // If we get that event it means that our user has been successfully added
        **this.dataChangedEvent.emit();** // now let the interested component(s) know
        this.navigateToView(user);
      }
    )
  } else {
    this.dataService.updateUser(this.formUser).subscribe(
      (user) => {
        // If we get that event it means that our user has been successfully updated
        **this.dataChangedEvent.emit();** // now let the interested component(s) know
        this.navigateToView(user);
      }
    );
  }
}
```

That should be telling the parent component that we want it to do something. Now we go the the parent component and bind to this event.

### in users.component.html

```html
<app-user-edit *ngIf="action === 'edit' || action === 'add'" 
			 [user]="selectedUser"
       **(dataChangedEvent)="loadData()"**>
</app-user-edit>
```

To complete with the update process we should deal with what happens if there’s an error or if the server doesn’t respond. We think that we should display a message to the user.

### in user-edit.component.ts

```tsx
onSubmit(): void {
  **this.message = 'saving...'**;
  if (this.formUser.id == null) {
    this.dataService.addUser(this.formUser, this.password).subscribe(
      (user) => {
        this.dataChangedEvent.emit();
        this.navigateToView(user);
      },
      **(error) => {
        this.message = 'Something went wrong and the user wasn\'t added. You may want to try again';
      }**
    )
  } else {
    this.dataService.updateUser(this.formUser).subscribe(
      (user) => {
        this.dataChangedEvent.emit();
        this.navigateToView(user);
      },
      **(error) => {
        this.message = 'Something went wrong and the user wasn\'t updated. You may want to try again';
      }**
    );
  }
}
```

## 163 Customizing data with JavaScript objects (5m)

Let’s now have a look at adding a new user. Now we have an issue here which is that the add user screen lets us provide a password for the user. But the way we’ve coded up the backend, we’re providing an AngularUser to the backend and then in the method that converts the AngularUser to User (that’s the asUser method in AngularUser) we’re setting the password. Because we want to be able to provide a password in the frontend, we’ll need to change the backend so it works in a different way. You can have only one @RequestBody value in a REST method. We can not use an additional RequestBody parameter for the password. So we’ll need to provide the entire object including the password as the data type. We’ll actually say that the PostMapping adding a new user can accept a regular User object, not an AngularUser object.

### in RestUserController.java

```java
@PostMapping
public AngularUser addUser(@RequestBody **User** newUser) {
    return new AngularUser(userRepository.save(newUser));
}
```

What we’ll need to do is find a way to send an object of type User to the backend. The difference between our AngularUser for the frontend User object is that as well as having an id and a name there is an extra field called a password, that we’ll want to send through to the backend. This is a good example of when we don’t need to start creating another object type in Angular. We don’t need to start creating User with password for example as a different class. Instead we can make use of Java objects. Because what we are sending is JSON if we create a Java object that’s not based from a class that when it’s serialized provides the JSON in the right format, then our backend will be quite happy to accept that as a backend User object and will be able to deserialize it. What we’ll want to do is create the object first and send it in our data service.

### in data.service.ts

```tsx
addUser(newUser: User, userPassword: string): Observable<User> {
  const fullUser = {
    id: newUser.id,
    name: newUser.name,
    password: userPassword
  };
  return this.http.post<User>(environment.restUrl + '/api/users', 
															fullUser)
    .pipe(map(data => User.fromHttp(data)));
}
```

## 164 Exercise 3 - POST and PUTting data (2m)

Implement Room editing and adding

- Code up the data service methods
- Use events to reload the data
- Display a message to the user
- Handle errors

## 165 Exercise 3 - Solution walkthrough (12m)

Edit rooms:

### in data.service.ts

```tsx
updateRoom(room: Room): Observable<Room> {
  const correctedRoom = {
    id: room.id,
    name: room.name,
    location: room.location,
    capacities: []
  }
  for (const lc of room.capacities) {
    let correctLayout;
    for (let member in Layout) {
      if (Layout[member] === lc.layout) {
        correctLayout = member;
      }
    }
    const correctedLayout = {
      layout: correctLayout,
      capacity: lc.capacity
    }
    correctedRoom.capacities.push(correctedLayout);
  }
  return this.http.put<Room>(environment.restUrl + '/api/rooms', 
															correctedRoom)
    .pipe(map(data => Room.fromHttp(data)));
}
```

### in room-edit.component.ts

```tsx
**@Output()
dataChangedEvent = new EventEmitter();**
message = '';

onSubmit() {
	// ...
  if (this.room.isNew()) {
    // ...
  } else {
    this.dataService.updateRoom(this.room).subscribe(
      next => {
        **this.dataChangedEvent.emit();**
        this.navigateToView(next);
      },
      error => this.message = 
					'Something went wrong, you may wish to try again.'
    );
  }
}
```

### in room.component.html

```html
<app-room-edit *ngIf="action === 'edit'" [room]="selectedRoom"
                    (dataChangedEvent)="loadData()"></app-room-edit>
```

Add rooms:

### in data.service.ts

```tsx
addRoom(newRoom: Room): Observable<Room> {
  return this.http.post<Room>(environment.restUrl + '/api/rooms', 
															this.getCorrectedRoom(newRoom))
    .pipe(map(data => Room.fromHttp(data)));
}

private getCorrectedRoom(room: Room) {
  const correctedRoom = {
    id: room.id,
    name: room.name,
    location: room.location,
    capacities: []
  }
  for (const lc of room.capacities) {
    let correctLayout;
    for (let member in Layout) {
      if (Layout[member] === lc.layout) {
        correctLayout = member;
      }
    }
    const correctedLayout = {
      layout: correctLayout,
      capacity: lc.capacity
    }
    correctedRoom.capacities.push(correctedLayout);
  }
  return correctedRoom;
}
```

### in room-edit.component.ts

```tsx
onSubmit() {
  // ...
  if (this.room.isNew()) {
    this.dataService.addRoom(this.room).subscribe(
      next => {
        **this.dataChangedEvent.emit();**
        this.navigateToView(next);
      },
      error => this.message = 
									'Something went wrong, you may wish to try again.'
    );
  } else {
    //...
  }
}
```

## 166 Bug fixing (3m)

Use `?` after a variable that may be undefined: `<td>{{ user?.id }}</td>`

# Chapter 32 - DELETing data and completing the case study (28m)

## 167 Implementing DELETE (5m)

In the last 2 chapters of this module we’re going to complete the case study. In this 1st chapter we’ll implement a couple of the remaining features for users in rooms. Let’s work on users and rooms together. We still need to implement the functionality to delete a room ,and to delete a user, and reset a users password. We’ll start with deleting a room.

### in RestRoomController.java

```java
@DeleteMapping("/{roomId}")
public void deleteRoom(@PathVariable Long roomId) {
    roomRepository.deleteById(roomId);
}
```

### in CorsConfig.java

```java
@Override
public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/api/**")
            .allowedMethods("GET", "POST", "PUT", **"DELETE"**)
            .allowedOrigins("http://localhost:4200");
}
```

### in data.service.ts

```tsx
deleteRoom(id: number): Observable<any> {
  return this.http.delete(environment.restUrl + '/api/rooms/' + id);
}
```

### in room-detail.component.ts

```tsx
@Output()
dataChangedEvent = new EventEmitter();
message = '';

deleteRoom() {
  this.message = 'Deleting...';
  this.dataService.deleteRoom(this.room.id).subscribe(
    next =>  {
      this.dataChangedEvent.emit();
      this.router.navigate(["admin", "rooms"]);
    }
  ), error => {
    this.message = 
					'Sorry - this room cannot be deleted at this time.';
  }
}
```

### in room-detail.component.html

```html
<div class="bg-warning p-5" *ngIf="message">{{ message }}</div>
```

### in rooms.component.html

```html
<app-room-detail *ngIf="action === 'view'" [room]="selectedRoom"
                  (dataChangedEvent)="loadData()"></app-room-detail>
```

## 168 Completing the users component (7m)

### in RestUserController.java

```java
@DeleteMapping("/{userId}")
public void deleteUser(@PathVariable Long userId) {
    userRepository.deleteById(userId);
}
```

Resetting the user’s password is a function we want to do, but there’s no obvious REST verb or URL that we should use. So we’re gonna have to decide what’s sensible for our API. To call resetting a password, we’re going to pass in the ID of the user that we want to take the password and reset it, but we’re not going to be sending any other data to the server. And for that reason we actually think it sits best with a GET request. You might disagree and think it really should be a PUT. If you want to make it a PUT, that’s absolutely fine. We’re going to make it a GET request.

### in RestUserController.java

```java
@GetMapping("/resetPassword/{userId}")
public void resetPassword(@PathVariable Long userId) {
    User user = userRepository.findById(userId).orElseThrow();
    user.setPassword("secret");
    userRepository.save(user);
}
```

### in data.service.ts

```tsx
deleteUser(id: number): Observable<any> {
  return this.http.delete(environment.restUrl + '/api/users/' + id);
}

resetUserPassword(id: number): Observable<any> {
  return this.http.get(environment.restUrl 
											+ '/api/users/resetPassword/' + id);
}
```

### in user-detail.component.ts

```tsx
@Output()
dataChangedEvent = new EventEmitter();
message = '';

deleteUser(): void {
  this.message = 'Deleting...';
  this.dataService.deleteUser(this.user.id).subscribe(
    next => {
      this.dataChangedEvent.emit();
      this.router.navigate(['admin', 'users'])
    },
    error => this.message = 
							'Sorry, this user cannot be deleted at this time.'
  )
}

resetPassword(): void {
  this.message = 'Please wait...';
  this.dataService.resetUserPassword(this.user.id).subscribe(
    next => this.message = 'The password has been reset.',
    error => this.message = 'Sorry, something went wrong.'
  );
}
```

### in user-detail.component.html

```html
<div class="bg-warning p-5" *ngIf="message">{{ message }}</div>
```

### in users.component.html

```html
<app-user-detail *ngIf="action === 'view'" [user]="selectedUser"
                  **(dataChangedEvent)="loadData()"**></app-user-detail>
```

## 169 Exercise 4 - Implementing REST from Angular (2m)

Implement

- Get all bookings by date
- Cancel a booking

Use: `Date sqlDate = Date.valueOf(date);`

## 170 Exercise 4 - Solution walkthrough (12m)

Get all bookings by date.

### in RestBookingsController.java

```java
@RestController
@RequestMapping("/api/bookings")
public class RestBookingController {
    private final BookingRepository bookingRepository;

    public RestBookingController(BookingRepository bookingRepository) {
        this.bookingRepository = bookingRepository;
    }

    @GetMapping("/{date}")
    public List<Booking> getBookingsByDate(@PathVariable String date) {
        return bookingRepository.findAllByDate(Date.valueOf(date));
    }

    @DeleteMapping("/{bookingId}")
    public void deleteBooking(@PathVariable Long bookingId) {
        bookingRepository.deleteById(bookingId);
    }
}
```

### in data.service.ts

```tsx
getBookings(date: string): Observable<Array<Booking>> {
  return this.http.get<Array<Booking>>(environment.restUrl 
																			+ '/api/bookings/' + date)
    .pipe(map(data => {
      const bookings = new Array<Booking>();
      for (const bookingData of data) {
        bookings.push(Booking.fromHttp(bookingData));
      }
      return bookings;
    }));
}
```

Implement the delete functionality.

### in data.service.ts

```tsx
deleteBooking(bookingId: number): Observable<any> {
  return this.http.delete(environment.restUrl + '/api/bookings/' 
																							+ bookingId);
}
```

### in calendar.component.ts

```tsx
ngOnInit(): void {
  this.loadData();
}

private loadData(): void {
  this.message = 'Loading data...';
  this.route.queryParams.subscribe(
    params => {
      this.selectedDate = params['date'];
      if (!this.selectedDate) {
        this.selectedDate = formatDate(new Date(), 'yyy-MM-dd', 'en');
      }
      this.dataService.getBookings(this.selectedDate).subscribe(
        next => {
          this.bookings = next;
          this.dataLoaded = true;
          this.message = '';
        },
        error => {
          this.message = 'Sorry - the data could not be loaded.'
        }
      )
    }
  )
}

deleteBooking(bookingId: number) {
  this.message = 'Deleting, please wait...';
  this.dataService.deleteBooking(bookingId).subscribe(
    next => {
      this.loadData();
    },
    error => {
      this.message = 'Sorry there was a problem deleting the item';
    }
  );
}
```

## 171 Creating a confirmation before deleting (2m)

`const deleteConfirmed = confirm('Are you sure you wish to delete this room?');`

# Chapter 33 - Pre-fetching data (37m)

## 172 Setting up the edit booking functionality (6m)

We’re going to envounter a slight complication which will required the final feature of Angular that we’re going to be covering in this module, which is pre-fetching data.

### in RestBookingsController.java

```java
@DeleteMapping("/{bookingId}")
public void deleteBooking(@PathVariable Long bookingId) {
    bookingRepository.deleteById(bookingId);
}
```

## 173 The concept of pre-fetching data (4m)

We’ll find out the reason for this issue if we look at the code. We’re seeing that the data has been loaded when we’ve got the booking data. But actually, we also want to make sure we’ve got the values for rooms and users before we start displaying this data. The issue we’ve got then is that the rooms is taking some time to come through. It is taking too long. The booking data is available and the “Edit Booking” page is being displayed before we’ve loaded up the users. These drop-downs are being populated with the list of users and the list of rooms, but that population of this drop-down data is happening after the booking has been put onto this page. And that’s why the existing booking’s room has not matched one of these rooms and the existing booking’s user has not matched one of these users.

The issue then is that we really want to make sure we’ve got all of this data, not just the booking data, before we consider the data to be loaded. A solution is going to be to pre-fetch the data. To get all the rooms and all the users from the backend, before we even start in this component.

There’s a production standard way to do that, but we’ll do a non-production standard way first, just so that you understand exactly the idea of what we’re trying to achieve. So we wouldn’t do it this way in production and we are going to move to a better way of doing things in a few moments time, but the idea is this.

Right now the flow is that the user will be in the calendar component, they’ll click a button and they’ll be navigating to the edit component. We’re going to change this navigation. We’ll no longer directly navigate from calendar to edit. Instead we’re going to create a new component, some interim component that will sit between the two, and the job of this component is to load the data. And we’ll call it for now the *LoadData* component. So when the user clicks on either a new booking or to an amend a booking, from the calendar component we’ll navigate them first of all to this *LoadData* component. In the ngOnInit method of this component what we’ll do is set up a service and in the service we’ll load up the data, we’ll get all the rooms and all the users and we’ll store those rooms and users in the service. Once we’ve got all those, then we’ll navigate automatically to the edit component and the edit component can get the users and the rooms from the service. That data will have been pre-fetched, it will be available for the edit component, so there’ll be none of the issues we’re seeing so far. That data will exist before we even get to the edit component. That’s the idea of doing things in this manual, not quite production standard way.

## 174 Pre-fetching data with navigation and a service (15m)

We’re going to be creating a new component and a new service.

Service:`ng g s EditBookingData`

Component: `ng g c calendar/BookingEditLoad`

### in edit-booking-data.service.ts

```tsx
export class EditBookingDataService {
  rooms: Array<Room>;
  users: Array<User>;
  dataLoaded = 0;

  constructor(private dataService: DataService) {
    this.dataService.getRooms().subscribe(
      next => {
        this.rooms = next;
        this.dataLoaded++;
      }
    )
    this.dataService.getUsers().subscribe(
      next => {
        this.users = next;
        this.dataLoaded++;
      }
    )
  }

  bookingDataIsLoaded(): boolean {
    return this.dataLoaded === 2;
  }
}
```

This isn’t going to be the final solution.

### in booking-edit-load.component.ts

```tsx
export class BookingEditLoadComponent implements OnInit {

  constructor(private bookingEditDataService : EditBookingDataService,
              private router: Router,
              private route: ActivatedRoute) { }

  ngOnInit(): void {
    setTimeout(() => this.navigateWhenReady(), 1000);
  }

  navigateWhenReady(): void {
    // check to see if the service data is loaded
    if (this.bookingEditDataService.bookingDataIsLoaded()) {
      // if yes - we'll navigate to the edit component
      const bookingId = this.route.snapshot.queryParams['id'];
      if (bookingId) {
        this.router.navigate(['bookingEdit'], 
														{queryParams: {id: bookingId}});
      } else {
        this.router.navigate(['bookingAdd']);
      }
    } else {
      // if not - wait 500ms then try again
      setTimeout(() => this.navigateWhenReady(), 500);
    }
  }
}
```

### in booking-edit.component.ts

```tsx
ngOnInit(): void {
  this.rooms = this.bookingEditDataService.rooms;
  this.users = this.bookingEditDataService.users;

  const id = this.route.snapshot.queryParams['id'];
  if (id) {
    const idNumber = +id;
    this.dataService.getBookingById(idNumber)
      .pipe(map(
        booking => {
          booking.room = this.rooms.find(room => room.id === booking.room.id);
          booking.user = this.users.find(user => user.id === booking.user.id);
          return booking;
        }
      ))
      .subscribe(next => {
        this.booking = next;
        this.dataLoaded = true;
        this.message = '';
      });
  } else {
    this.booking = new Booking();
    this.dataLoaded = true;
    this.message = '';
  }
}
```

### in calendar.component.ts

```tsx
editBooking(bookingId: number) {
  this.router.navigate(['**bookingEditLoad**'], 
												{queryParams: {id: bookingId}});
}

addBooking() {
  this.router.navigate(['**bookingEditLoad**']);
}
```

### in app.module.ts

```tsx
const routes: Routes = [
  {path : 'admin/users', component : UsersComponent},
  {path : 'admin/rooms', component : RoomsComponent},
  {path : '', component : CalendarComponent},
  {path : 'bookingEdit', component : BookingEditComponent},
  **{path : 'bookingEditLoad', component : BookingEditLoadComponent},**
  {path : 'bookingAdd', component : BookingEditComponent},
  {path : '404', component : PageNotFoundComponent},
  {path : '**', redirectTo: '/404'}
];
```

## 175 Using a resolver (11m)

So what is the problem? Let’s imagine we clicked to amend a room. We waited for the data to load, we’ve got the data here, we’ve probably started making some changes. And then we thought, ah, these changes aren’t right. We know, we’ll refresh the page to get the data back and then start editing again. But we’ll get an error. If we’re refreshing the page, we’re going directly to a URL “bookingEdit?id=123”, but actually we never want to be able to do that. We always want to go via a URL “bookingEditLoad?id=123”. If we go via that URL then all the data’s going to become available and we’ll be able to refresh the page.

The problem with the approach we’ve set up is that we end up at a URL that we don’t want the user ever to be able to refresh the browser for. We can’t stop the users refreshing the browser, so we need an alternative approach. The production standard way to do what we’re trying to do here to pre-fetch data is to use what’s called a *resolver*.

A *resolver* is an object which resolves an Observable. It’s another way of working with the Observer Design Pattern. The resolver does the subscribing part and then it waits for the data to become available. In coding terms, a resolver is a service that implements an interface called the *Resolve* Interface. In fact the code is more straightforward than what we’ve just done. What we’ll be doing now is a shortcut feature.

We’re going to replace the EditBookingDataService with two new services, one to load up the rooms and one to load up the users.

`ng g s PrefetchRooms`

### in prefetch-rooms.service.ts

```tsx
export class PrefetchRoomsService 
			implements Resolve<Observable<Array<Room>>>{
  constructor(private dataService: DataService) { }

  resolve(): Observable<Array<Room>> {
    return this.dataService.getRooms();
  }
}
```

`ng g s PrefetchUsers`

### in prefetch-users.service.ts

```tsx
export class PrefetchUsersService 
				implements Resolve<Observable<Array<User>>>{
  constructor(private dataService: DataService) { }

  resolve(): Observable<Array<User>> {
    return this.dataService.getUsers();
  }
}
```

Now that we’ve created these two resolvers, we don’t need the service that we added and the component that we added. Before we delete them, we’re going to change the routing back in the calendar.component.

### in calendar.component.ts

```tsx
editBooking(bookingId: number) {
  this.router.navigate(['**bookingEdit**'], 
					{queryParams: {id: bookingId}});
}

addBooking() {
  this.router.navigate(['**bookingAdd**']);
}
```

And we remove from app.module.ts this navigation line:

`{path : 'bookingEditLoad', component : BookingEditLoadComponent},`

The we can delete both load component and service.

We are navigating directly from Calendar to Edit, but the idea is that as part of that navigation, before we reach the Edit component, we want to populate the rooms array and the users array. Right now they are not being populated. In fact these Resolvers are going to do that population for us, the two services we’ve just created. We’re going to put these resolvers as part of the navigation instruction within app.module.

Now we have `{path : 'bookingEdit', component : BookingEditComponent},`

The idea is that we’re adding in some extra parameters to this path. It will the Angular’s responsibility to get a list of rooms and a list of users and make them available before we reach the `BookingEditComponent`. We need to do the same for “bookingAdd” route.

### in app.modules.ts

```tsx
const routes: Routes = [
  {path : 'admin/users', component : UsersComponent},
  {path : 'admin/rooms', component : RoomsComponent},
  {path : '', component : CalendarComponent},
  {
    path : 'bookingEdit',
    component : BookingEditComponent,
    **resolve : {
      rooms : PrefetchRoomsService,
      users : PrefetchUsersService
    }**
  },
  {
    path : 'bookingAdd', 
    component : BookingEditComponent,
    **resolve : {
      rooms : PrefetchRoomsService,
      users : PrefetchUsersService
    }**
  },
  {path : '404', component : PageNotFoundComponent},
  {path : '**', redirectTo: '/404'}
];
```

The final step is that when we do finally reach BookingEditComponent, the rooms and users should be available for us, but we need to go and get them. They’ll be in the ActivatedRoute object in a parameter called *data*.

### in booking-edit.component.ts

```tsx
ngOnInit(): void {
  this.rooms = this.route.snapshot.data['rooms'];
  this.users = this.route.snapshot.data['users'];
	// ...
}
```

Now that should be everything done.

Recap one more time. When we navigate to bookingAdd or bookingEdit, before the component is loaded, these resolvers are going to be set up so that in the route it’s a bit like having the ID as a parameter, we’re now gonna have the rooms and the users available. We won’t see them, they’ll be hidden, but we can access them from the *route* within our component.

## 176 Module summary (1m)

There is one piece of functionality to implement which is the Save button. This will be in the code for this chapter.