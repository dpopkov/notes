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

Let’s now have a look at adding a new user. Now we have an issue here which is that the add user screen lets us provide a password for the user. But the way we’ve coded up the backend, we’re providing an AngularUser to the backend and then in the method *newUser* in *RestUserController* we convert an AngularUser to a User and we’re setting the password in the *asUser()* method. Because we want to be able to provide a password in the frontend, we’ll need to change the backend so it works in a different way. You can have only one @RequestBody value in a REST method. We can not use an additional RequestBody parameter for the password. So we’ll need to provide the entire object including the password as the data type. We’ll actually say that the PostMapping adding a new user can accept a regular User object, not an AngularUser object.

164 Exercise 3 - POST and PUTting data (2m)

165 Exercise 3 - Solution walkthrough (12m)

166 Bug fixing (3m)

# Chapter 32 - DELETing data and completing the case study (28m)

# Chapter 33 - Pre-fetching data (37m)