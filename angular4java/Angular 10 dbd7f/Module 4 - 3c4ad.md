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

# Chapter 33 - Pre-fetching data (37m)