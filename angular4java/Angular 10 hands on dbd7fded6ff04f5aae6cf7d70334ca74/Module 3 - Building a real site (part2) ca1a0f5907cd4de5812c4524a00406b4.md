# Module 3 - Building a real site (part2)

# Chapter 23 - Template form validation (28m)

## 117 Dynamic classes (CSS) applied to form elements (7m)

What we’ve built so far looks ok, but it would be nice to put some validation in here. And then these error message placeholders that we have can be put to some use. We won’t allow the name field to be blank and also the password field if we are entering it shouldn’t be blank also. We should probably make it so the user’s password is entered twice, and two values should match. So let’s just first adjust the form to have two password fields and then we can learn how to do validation. We create variable in TypeScript and an HTML element for it.

### in user-edit.component.ts

```tsx
passwordDouble: string;
```

### in user-edit.component.html

```html
<div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name" 
						placeholder="user name"
           [(ngModel)]="formUser.name" name="name" **required**>
    <div class="alert alert-danger">error message placeholder</div>
  </div>
```

```html
<div class="form-group" *ngIf="user.id == null">
  <label for="password">Password</label>
  <input type="password" class="form-control" id="password" 
				placeholder="password"
        [(ngModel)]="password" name="password">
  <div class="alert alert-danger">error message placeholder</div>
  **<label for="password2">Repeat password</label>
  <input type="password" class="form-control" id="password2" 
				 placeholder="password"
         [(ngModel)]="passwordDouble" name="password2">
  <div class="alert alert-danger">error message placeholder</div>**
</div>
```

Let’s start with the first peace of validation which is going to be on the name field. Because we’re working at HTML here and we want this value to be filled in the simplest thing we could do is to particularly require that on the HTML element. We just add the attribute *required*. This is a standard HTML attribute. It’s nothing to do with Angular. And if we go and try this now then Angular will allow to do that. It looks like this *required* element doesn’t work. Actually it is not that it doesn’t work. It does something much more clever in the background. Angular uses dynamic styles in this situation: `ng-valid` or `ng-invalid` - to indicate whether the entry in the form passes the validation rules, `ng-touched` or `ng-untouched` - to indicate whether the user has selected the element either with the mouse or has tabbed into it, `ng-pristine` or `ng-dirty` - to indicate whether the user has edited the value in the control. Let’s adjust our CSS for the component to make the input box have a red border if it’s not valid.

### user-edit.component.css

```css
input**.ng-invalid.ng-touched** {
  border: 1px solid red;
}
```

Input field now has the red border applied to if.

## 118 Validation messages and controlling the submit button (5m)

Let’s have a look at error message placeholders. It would be nice if these gave a hint to the user as to why the field might not be valid. The first thing we’ll do is only show these divs if the field above isn’t valid. In order to do that we’re going to use ngIf on these divs. We will only show the divs if the input field is not valid. In order to do that we need to reference the input field. We can put in a template reference. And we put some meaningful text in the placeholder now.

### in user-edit.component.html

```html
<div class="form-group">
  <label for="name">Name</label>
  <input type="text" class="form-control" id="name" 
				 placeholder="user name"
         [(ngModel)]="formUser.name" name="name" required 
				 **#nameField="ngModel"**>
  <div ***ngIf="nameField.invalid && nameField.touched"**
       class="alert alert-danger">**Name is required**</div>
</div>
```

Ideally we don’t want to be able to submit this form if the form isn’t valid. It would be sensible if we disable the *save* button if the form isn’t valid. We will use template reference for the form to check whether the *save* button should be disabled.

### in user-edit.component.html

```html
**<!-- Put the template reference on the form -->**
<form **#editUserForm="ngForm"**>
	...
  <button type="button" class="btn btn-primary" (click)="onSubmit()"
          **[disabled]="editUserForm.invalid"**
  >Save</button>
</form>
```

That is the basic validation of the name field set up and working.

## 119 Custom validation rules (5m)

Let’s do something a bit more complicated now, some custom validation. Ideally we want the name to contain more than just spaces. Now a name containing only spaces looks like a valid name, the *save* button is enabled, and we haven’t got any kinds of red hints, and if we click on *save* then the name will be changed to a blank name. We want to have some kind of custom validation that doesn’t allow the name to be blank. We’ll start by creating a variable called *nameIsValid*. Next we will create a function to determine whether or not the name is valid.

### in user-edit.component.ts

```tsx
**nameIsValid = false;**
// ..
ngOnInit(): void {
  this.formUser = Object.assign({}, this.user);
  **this.checkIfNameIsValid();**
}
// ..
**checkIfNameIsValid(): void {
  this.nameIsValid = this.formUser.name.trim().length > 0;
}**
```

### in user-edit.component.html

```html
<div class="form-group">
  <label for="name">Name</label>
  <!-- Use "change" event for validation -->
  <input type="text" class="form-control" id="name" 
				 placeholder="user name"
         **(change)="checkIfNameIsValid()"**
         [(ngModel)]="formUser.name" name="name" 
				 required #nameField="ngModel">
  <div *ngIf="nameField.invalid && nameField.touched"
       class="alert alert-danger">Name is required</div>
  <!-- Add another validation message for blank name field. -->
  **<div *ngIf="nameField.touched && !nameIsValid"
       class="alert alert-danger">Name cannot be blank</div>**
</div>

<button type="button" class="btn btn-primary" (click)="onSubmit()"
        [disabled]="editUserForm.invalid **|| !nameIsValid**"
>Save</button>
```

That is some basic custom validation.

## 120 Exercise 3 - Implementing custom validation (3m)

First let’s fix one error when we adding a new user and the *formUser.name* variable is undefined and cannot be used to check whether the name is valid:

### in user-edit.component.ts

```tsx
checkIfNameIsValid(): void {
  if (this.formUser.name) {
    this.nameIsValid = this.formUser.name.trim().length > 0;
  } else {
    this.nameIsValid = false;
  }
}
```

To finish this chapter we’d like to set you the exercise of implementing the 2nd set of validation rules:

- The password is required
- The password must not be blank
- The 2 password values must match

As a little hint, if the passwords aren’t valid then you’re going to be disabling the button on the form, of course, but you’ll only want to do that if we are adding a new user. If we’re not adding a user, then you don’t want to be disabling the button because the passwords aren’t valid. In edit user mode you don’t see the password fields, so in “edit” user mode we don’t care whether the password fields are valid or not. That shouldn’t impact whether or not we can click the save button. When we’re in “add” mode that’s when all of these should apply.

## 121 Exercise 3 - Solution walkthrough (9m) - mine

### in user-edit.component.ts

```tsx
passwordIsValid = false;
passwordsMatch = false;
// ..
ngOnInit(): void {
  this.formUser = Object.assign({}, this.user);
  this.checkIfNameIsValid();
  this.checkIfPasswordIsValid();
  this.checkIfPasswordsMatch();
}
// ..
checkIfPasswordIsValid(): void {
  if (this.user.isNotNew()) {
    this.passwordIsValid = true;
  } else if (this.password) {
    this.passwordIsValid = this.password.trim().length > 0;
  } else {
    this.passwordIsValid = false;
  }
}

checkIfPasswordsMatch(): void {
  if (this.user.isNotNew()) {
    this.passwordsMatch = true;
  } else if (this.password && this.passwordDouble) {
    this.passwordsMatch = this.password === this.passwordDouble;
  } else {
    this.passwordsMatch = false;
  }
}
```

### in user-edit.component.html

```html
<div class="form-group" *ngIf="user.isNew()">
  <label for="password">Password</label>
  <input type="password" class="form-control" id="password" 
				 placeholder="password"
         **(change)="checkIfPasswordIsValid(); checkIfPasswordsMatch()"**
        [(ngModel)]="password" name="password" required 
				**#passwordField="ngModel"**>
  **<div *ngIf="passwordField.invalid && passwordField.touched"
       class="alert alert-danger">Password is required</div>
  <div *ngIf="passwordField.touched && !passwordIsValid"
       class="alert alert-danger">Password cannot be blank</div>**

  <label for="password2">Repeat password</label>
  <input type="password" class="form-control" id="password2" 
				 placeholder="password"
         **(change)="checkIfPasswordsMatch()"**
         [(ngModel)]="passwordDouble" name="password2" required 
				 **#password2Field="ngModel"**>
  **<div *ngIf="password2Field.invalid && password2Field.touched"
       class="alert alert-danger">Re-typed password is required</div>
  <div *ngIf="password2Field.touched && !passwordsMatch"
       class="alert alert-danger">Passwords do not match</div>**
</div>

<button type="button" class="btn btn-primary" (click)="onSubmit()"
        [disabled]="editUserForm.invalid || !nameIsValid 
					**|| (user.isNew() && (!passwordIsValid || !passwordsMatch))"**
>Save</button>
```

# Chapter 24 - Reactive forms (33m)

## 122 Setting up navigation to show a form (8m)

What we learned about in the last 2 chapters is template driven forms. And this is the easier way to create forms in Angular. But sometimes we’ll have a requirement that just won’t work with this way of building forms. We need something more flexible and it turns out that this is going to be the case when we come to create the form to edit rooms.

What we are going to be doing in this chapter is building the edit room form with a part that’s going to be generated dynamically, based on the values of an enum.

To start let’s create a new component to edit rooms and we’ll also sort out the navigation to show this component as we did when we first started with editing users.

`ng g c admin/rooms/RoomEdit`

### in room-edit.component.ts

```tsx
export class RoomEditComponent implements OnInit {
  @Input()
  room: Room;

  ngOnInit(): void {
  }
}
```

We need a method that we can bind to the click event of the *edit* button. We call it *editRoom()*.

### in room-detail.component.ts

```tsx
constructor(private router: Router) { }
// ..
editRoom() {
  this.router.navigate(["admin", "rooms"], 
									{queryParams: { id: this.room.id, action: 'edit'} })
}
```

Now we want to bind this method to the click event of the edit room button.

### in room-detail.component.html

```html
<a class="btn btn-small btn-warning" **(click)="editRoom()"**>edit</a>
```

Most of the work then to do in navigation is going to work in the rooms component. In the TypeScript file, we don’t have the action there as a string yet. Let’s add that first. Then in the *ngOnInit()* method subscribe on the queryParams, we’re getting the ID, and we’ll also want to set the action at this point. If the action is “add” then we’ll want to set up a new room to work with the edit screen.

### in rooms.component.ts

```tsx
**action: string;**

ngOnInit(): void {
  // ...
  this.route.queryParams.subscribe(
    (params) => {
      const idAsString = params['id'];
      if (idAsString) {
        const idAsNumber = +idAsString;
        this.selectedRoom = this.rooms
													.find(room => room.id === idAsNumber);
      }
      this.action = params['action'];
      **if (this.action === 'add') {
        this.selectedRoom = new Room();
        this.action = 'edit';
      }**
    }
  )
}
setSelectedRoom(roomId: number): void {
  this.router.navigate(['admin', 'rooms'], 
											{queryParams : { id: roomId**, action: 'view'**}});
}
**addRoom(): void {
  this.router.navigate(['admin', 'rooms'], 
												{queryParams : { action: 'add'}});
}**
```

In the rooms.component.html we’ll start with *add* button. Then we add *app-room-edit* component.

### in rooms.component.html

```html
<a class="btn btn-warning mb-3" **(click)="addRoom()"**>add</a>
...
<div class="col-6">
  <app-room-detail ***ngIf="action === 'view'"** [room]="selectedRoom">
	</app-room-detail>
  **<app-room-edit *ngIf="action === 'edit'" [room]="selectedRoom">
	</app-room-edit>**
</div>
```

That’s navigation all set up.

## 123 Creating a reactive form (10m)

To build this form we use prepared HTML for this chapter.

### in room-edit.component.html

```html
<h1> {{room.isNew() ? 'Add' : 'Edit'}} Room</h1>

<form>
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name" 
					 placeholder="room name" >
    <div class="alert alert-danger"></div>
  </div>

  <div class="form-group">
    <label for="location">Location</label>
    <input type="text" class="form-control" id="location" 
					 placeholder="location" >
    <div class="alert alert-danger"></div>
  </div>

  <h2>This room can accomodate:</h2>

  <!-- list the layout types here -->

  <button type="submit" class="btn btn-primary">Save</button>
</form>
```

The way that we’re going to bind the code to the form is going to be very different this time around. We’ll be using the alternative approach which is called *reactive forms*. For this to work, the first thing we need to do is we need to import another module in our *app.module.ts* file. In order to use template driven forms we had to have the FormsModule. To use reactive forms we need to have the **ReactiveFormsModule**. You can absolutely have both of these in the same project. That’s fine.

### in app.module.ts

```tsx
imports: [
    BrowserModule,
    FormsModule,
    **ReactiveFormsModule**,
    RouterModule.forRoot(routes)
  ],
```

Now we are ready to start creating our form. The principle here is that for each of the input elements on the form (and at the moment there’s just two, there’s one for the room name, and one for the location, we’re going to deal with the layouts a little bit later on), but for these two input elements we’re going to define a variable in the TypeScript file that’s going to reference that input. The variable is going to be an object of type **FormControl**. Let’s start with the name field. This won’t be the full picture just yet. We’re going to create a variable called *roomName* and set up equal to new object of type FormControl. The label of FormControl is going to match a label we’ll place on the HTML. For now let’s put “roomName” as our label.

### in room-edit.component.ts

```tsx
roomName = new FormControl('**roomName**');
```

### in room-edit.component.html

```html
<input type="text" class="form-control" id="name" 
						placeholder="room name"
            **[formControlName]="roomName"**>
```

This is the theory of how we are going to bind data in the backing file to the input elements in HTML. This by itself, however, won’t work. You actually can’t create a FormControl element like this. It needs to sit within what’s called a form group object, or in other words, we need to create an object that represents and is bound to the form as well.

If we start with a form object, we’re going to take its **formGroup** property and bind it to a variable that we’ll create in the backing file.

In the HTML we have a form and within the form are going to be some controls. We’re replicating this same structure, the form and it’s controls in code. And as we do that, we bind the form to the forming code and we bind each of the input elements to the relevant control in code.

Whereas in the last way that we brought forms in template driven forms, we bound our fields to attribute in the class directly, either properties of an object or just a string that we might have declared at the class level.

Here, we’re binding our form fields to programmatic representations of those fields. We’re binding them to objects of type FormControl. The syntax is that we give the form object a FormGroup and we’re using square brackets here because we’re binding it to a FormGroup object in the backing file. And our FormGroup object will have multiple parameters and these are the key-value pairs that define the controls. So, we’ve got one control so far, which has a key of “roomName” and a value, but it’s a FormControl with a label of “roomName” and we’ve said this is the roomName for this particular object.

### in room-edit.component.ts

```tsx
**roomForm = new FormGroup(
  {**
    roomName: new FormControl('roomName')
  }
);
```

### in room-edit.component.html

```html
<form **[formGroup]="roomForm"**>
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name" 
						placeholder="room name"
            **formControlName="roomName"**>
    <div class="alert alert-danger"></div>
	</div>
  ...
</form>
```

Let’s create the location in the same way.

### in room-edit.component.ts

```tsx
roomForm = new FormGroup(
  {
    roomName: new FormControl('roomName'),
    **roomLocation: new FormControl('location')**
  }
);
```

### in room-edit.component.html

```html
<div class="form-group">
  <label for="location">Location</label>
  <input type="text" class="form-control" id="location" 
				 placeholder="location"
         **formControlName="location"**>
  <div class="alert alert-danger"></div>
</div>
```

There is clearly going to be more to do here, but so we can see this is working, we’re going to code up an *onSubmit()* method and in that method we’ll just output the form group object in the console, so we can explore it.

### in room-edit.component.ts

```tsx
onSubmit() {
  console.log('this.roomForm', this.roomForm);
}
```

### in room-edit.component.html

```html
<button type="button" class="btn btn-primary" (click)="onSubmit()"
>Save</button>
```

We also add attribute *required* to both input elements.

## 124 Setting and reading form control values (4m)

Right now we’ve bound our HTML to the FormGroup object, but we don’t yet have any of the data from our room appearing in the form. You can probably guess that what we’ll need to do is to place the data from the room into the controls when the form loads and then in onSubmit method, we’ll read the data out of the form and put it back into our room object. So how do we get the data into the form? You can probably guess we’ll do something in ngOnInit method. The FormGroupObject has a method called *patchValue()* which allows us to take each of the labels and provide a value for each label. And now when we load up the form we’ll see the values from our room appearing in that form.

### in room-edit.component.ts

```tsx
ngOnInit(): void {
  this.roomForm.patchValue(
    {
      roomName: this.room.name,
      roomLocation: this.room.location
    }
  );
}
```

Unlike in the template forms way of working with forms, we don’t have the problem here of needing to create a copy of our room object. The form is completely disconnected from it. When we come to save the form we’ll need to extract the values form the FormGroup at that point. We do that in the *onSubmit()* method

### in room-edit.component.ts

```tsx
onSubmit() {
  this.room.name = this.roomForm.controls['roomName'].value;
	// other way
  this.room.location = this.roomForm.value['roomLocation']; 
  console.log('updated room:', this.room);
}
```

Now we’re ready to save this room object. You’ll notice that because of the way our application works, the room is being automatically updated in the backend. But we’ll ignore that and do it properly. What we really want to do here is to call some method in the data service to save the room at this point. We’re going to come back and do that in a little while.

## 125 Programmatically adding controls (11m)

Let’s now talk about the reason why we have had to switch to the active forms. And that is that we don’t know at design time how many layout options there are going to be. So we want to build up a set of values in the corresponding section of the page. It’s going to be a set of labels and inputs, one for each of the different layout types as defined in the enum, but at coding time we don’t know how many there are going to be, because that could change at some point in the future. We’re going to create some kind of loop to generate these controls. Let’s start building this and we’ll see what it looks like.

The first thing we’ll need to get is a list of the different layouts. Remember that in the model the layouts are an enum and they have keys and values. You might remember that we should be able to get an array of the keys by doing `layouts = Object.keys(Layout);`. 

### in room-edit.component.ts

```tsx
layouts = Object.keys(Layout);
```

That will allow us to create a repeating div in the HTML.

### in room-edit.component.html

```html
<div class="form-group" *ngFor="let layoutKey of layoutKeys">
  <label>{{layoutMap.get(layoutKey)}}</label>
  <input type="number" class="form-control">
</div>
```

The next thing we’ll want to do is make sure that each of these inputs have a unique ID. We’ll give them an ID, which will be the word ‘layout’ followed by the layout option. And we add formControlName as well

### in room-edit.component.html

```html
<div class="form-group" *ngFor="let layoutKey of layoutKeys">
    <label **for="layout{{layoutKey}}"**
		>{{layoutMap.get(layoutKey)}}</label>
    <input type="number" class="form-control" 
			**id="layout{{layoutKey}}"** **formControlName="layout{{layoutKey}}"**>
  </div>
```

That is the HTML part of the form setup. Next we want to create the same matching fields in the backing TypeScript file so that we can link the two together.

To do that what we’ll want to do is dynamically add in some extra controls into the controls array of our FormGroup called roomForm. We can do that in ngOnInit method.

### in room-edit.component.ts

```tsx
ngOnInit(): void {
  this.roomForm.patchValue(
    {
      roomName: this.room.name,
      roomLocation: this.room.location
    }
  );
  **for (const layoutKey of this.layoutKeys) {
    let label = `layout${layoutKey}`;
    this.roomForm.addControl(label, new FormControl(label));
  }**
}
```

To finish this chapter we just go the the onSubmit method and we can extract now the values from these new form controls that we’ve added and add those into the room object.

### in room-edit.component.ts

```tsx
onSubmit() {
  this.room.name = this.roomForm.controls['roomName'].value;
	 // other way
  this.room.location = this.roomForm.value['roomLocation'];
  this.room.capacities = new Array<LayoutCapacity>();
  **for (const layoutKey of this.layoutKeys) {
    const layoutCapacity = new LayoutCapacity();
    layoutCapacity.layout = Layout[layoutKey];
    const label = `layout${layoutKey}`;  // using string interpolation
    layoutCapacity.capacity = this.roomForm.controls[label].value;
    this.room.capacities.push(layoutCapacity);
  }**
  console.log('updated room:', this.room);
  // todo: call a method in the data service to save the room
}
```

# Chapter 25 - Going further with Reactive Forms (21m)

## 126 Using the FormBuilder (7m)

In the last chapter we created a form dynamically. That is we created a form group with some form controls and then we wrote some code to add extra controls into that form group. Before we start adding in new functionality, we can simplify what we’ve already written. Angular gives us a way to make it a bit less code to type by using an object called a FormBuilder. The starting point is to inject a FormBuilder into our class. And then we’ll build the form in the ngOnInit method.

### in room-edit.component.ts

```tsx
**roomForm: FormGroup;**
constructor(**private formBuilder: FormBuilder**) {
	// ...
}

ngOnInit(): void {
  **this.roomForm = this.formBuilder.group(
      {
        roomName: this.room.name,  // control-name : initial-value
        roomLocation: this.room.location
      }
  );
  // don't need the this.roomForm.pachValue(..) method any more**
  for (const layoutKey of this.layoutKeys) {
    **const layoutCapacity = this.room.capacities
							.find(lc => lc.layout === Layout[layoutKey]);
    const initialCapacity = layoutCapacity == null ? 0 
														: layoutCapacity.capacity;**
    const label = `layout${layoutKey}`;
    this.roomForm.addControl(label, 
													**this.formBuilder.control(initialCapacity)**);
  }
}
```

## 127 Implementing validators (3m)

Next, let’s have a look at validation. We’d want to have similar to what we had in the user’s validation, that the room name and the location cannot be blank and must be filled in. It turns out for user reactive forms adding in validation rules is really simple. Angular gives us an object type called a Validator and there are quite a few built-in validators which we can use.

### in room-edit.component.ts

```tsx
ngOnInit(): void {
  this.roomForm = this.formBuilder.group(
      {
        roomName: [this.room.name, **Validators.required**],
        roomLocation: [this.room.location, 
											**[Validators.required, Validators.minLength(2)]**]
      }
  )
  // ...
}
```

## 128 Validation messages and controlling the submit button (11m)

The things we’ll need to do to finish this off are to display some validation errors on the room name and location fields, if appropriate. We’ll want to prevent the *save* button being clicked if there are validation errors. And we’ll also want to implement the save function by calling a method in the data service.

Let’s start by completing the room edit component.

### in room-edit.component.html

```html
<div class="form-group">
  ...
  <div class="alert alert-danger"
       *ngIf="roomForm.controls['roomName'].invalid && roomForm.controls['roomName'].dirty"
  >Room name cannot be blank</div>
</div>
<div class="form-group">
  ...
  <div class="alert alert-danger"
       *ngIf="roomForm.controls['roomLocation'].invalid && roomForm.controls['roomLocation'].dirty"
  >Room location cannot be blank</div>
</div>
...
<button type="button" class="btn btn-primary" (click)="onSubmit()"
        [disabled]="roomForm.invalid"
>Save</button>
```

And the final step is going to be saving the changes back into the data service.

### in data.service.ts

```tsx
updateRoom(room: Room): Observable<Room> {
  const originalRoom = this.rooms.find(r => r.id === room.id);
  if (originalRoom) {
    originalRoom.name = room.name;
    originalRoom.location = room.location;
    originalRoom.capacities = room.capacities;
  }
  return of(originalRoom);
}

addRoom(newRoom: Room): Observable<Room> {
  let maxId = 0;
  for (const r of this.rooms) {
    if (r.id > maxId) {
      maxId = r.id;
    }
  }
  newRoom.id = maxId + 1;
  this.rooms.push(newRoom);
  return of(newRoom);
}
```

Now let’s deal with the *onSubmit()* method.

### in room-edit.component.ts

```tsx
onSubmit() {
  //...
  if (this.room.isNew()) {
    this.dataService.addRoom(this.room).subscribe(
      next => {
        this.navigateToView(next);
      }
    );
  } else {
    this.dataService.updateRoom(this.room).subscribe(
      next => {
        this.navigateToView(next);
      }
    );
  }
}
private navigateToView(next: Room) {
  this.router.navigate(['admin', 'rooms'], 
											{queryParams: {action: 'view', id: next.id}});
}
```

# Chapter 26 - Completing the case study (1h15m)

## 129 Fixing a lifecycle bug

There is a small bug in the rooms and users functionality which we want to fix. This bug is quite typical of the kind of thing that can happen when we develop an Angular application.

If we are editing an existing user or room but I then choose to go and add a new user we get to the add user screen but the name of the user that we are editing has been left behind in the name input field.

We’ll create FormResetService to fix this issue: `ng g s FormReset`.

### form-reset.service.ts

```tsx
export class FormResetService {
  resetRoomFormEvent = new EventEmitter<Room>();
  resetUserFormEvent = new EventEmitter<User>();
}
```

### rooms.component.ts

```tsx
ngOnInit(): void {
  //...
  this.route.queryParams.subscribe(
    (params) => {
      // ...
      if (this.action === 'add') {
        this.selectedRoom = new Room();
        this.action = 'edit';
        **this.formResetService
						.resetRoomFormEvent.emit(this.selectedRoom);**
      }
    }
  )
}
```

### room-edit.component.ts

```tsx
ngOnInit(): void {
  this.initializeForm();
  this.resetEventSubscription = this.formResetService
																		.resetRoomFormEvent.subscribe(
    room => {
      this.room = room;
      this.initializeForm();
    }
  )
}
```

Do the same for users and user-edit components.

## 130 Implementing the delete functions

Before we start working on the Calendar component, there’s just a couple of bits of functionality we need to deal with. In the Rooms component there is a delete button that we haven’t yet implemented, and in the Users component there’s also a delete button, but there’s also *password reset* button. We can’t do much with this password reset. We will do it properly when we work with the REST data service when we’ve got our backend. But we’ll do something just to get some kind of click event on that button. Both delete and password reset will need to be calling methods in the data service.

### data.service.ts

```tsx
deleteRoom(id: number): Observable<any> {
  const room = this.rooms.find(r => r.id === id);
  this.rooms.splice(this.rooms.indexOf(room), 1);
  return of(null);
}
deleteUser(id: number): Observable<any> {
  const user = this.users.find(u => u.id === id);
  this.users.splice(this.users.indexOf(user), 1);
  return of(null);
}
```

### in user-detail.component.ts

```tsx
deleteUser(): void {
  this.dataService.deleteUser(this.user.id).subscribe(
    next => {
      this.router.navigate(['admin', 'users']);
    }
  )
}

resetPassword(): void {
  this.dataService.resetUserPassword(this.user.id).subscribe(
    next => {
      console.log("Reset is not implemented yet");
    }
  );
}
```

### in user-detail.component.html

```html
<a class="btn btn-small btn-warning" (click)="deleteUser()"
  >delete</a>
...
<td><a class="btn btn-small btn-warning"
       (click)="resetPassword()"
>reset</a></td>
```

### in room-detail.component.ts

```tsx
deleteRoom() {
  this.dataService.deleteRoom(this.room.id).subscribe(
    next =>  this.router.navigate(["admin", "rooms"])
  )
}
```

### in room-detail.component.html

```html
<a class="btn btn-small btn-warning" (click)="deleteRoom()">delete</a>
```

## 131 Displaying data with pipes for dates

Before we start working on the calendar component, which is going to be a task that I’m going to get you to do, we’ll set you a couple of exercises, there’s one last piece of functionality we think will be useful to know about. It is displaying dates in a specific format in Angular. This functionality is called Pipes. It allows us to change the way that something is displayed.

[The Pipes guide.](https://angular.io/guide/pipes)

### in calendar.component.html

```html
<p>The selected date is {{ selectedDate | date:'yyyy-MM-dd' }}</p>
<p>The selected month is {{ selectedDate | date:'MMM' }}</p>
<p>The selected month is {{ selectedDate | date:'MMMM' }}</p>
```

## 132 Exercise 4 - Displaying the calendar component

We’re finally ready to start working on the implementation of the calendar functionality.

We’ve split the job of building the calendar functionality into two parts. The first part is implementing the calendar component view functionality.

Display the calendar component:

- Create the model - a class representing a Booking (code provided)
- Create a getBookings() method in the data service, add dummy bookings
- Create the view screen and bind the data

## 133 Exercise 4 - Solution walkthrough

### in data.service.ts

```tsx
private bookings: Array<Booking> = new Array<Booking>();
// ...
getBookings(): Observable<Array<Booking>> {
  return of(this.bookings);
}
// ...
constructor() {
	// ...
  const today: string = formatDate(new Date(), 'yyyy-MM-dd', 'en-UK');
  const booking1 = new Booking(room1, user1, Layout.THEATER, 
								'Example Booking 1', today, '14:00', '15:00', 3, 201);
  const booking2 = new Booking(room2, user2, Layout.USHAPE, 
								'Example Booking 2', today, '15:00', '16:00', 5, 202);
  this.bookings.push(booking1);
  this.bookings.push(booking2);
}
```

### in calendar.component.ts

```tsx
bookings: Array<Booking>;

constructor(private dataService: DataService) { }

ngOnInit(): void {
  this.dataService.getBookings().subscribe(
    next =>  this.bookings = next
  )
}
```

### in calendar.component.html

```html
<h3 class="mt-5" *ngIf="bookings.length === 0">There are no meetings currently scheduled for this date.</h3>

<div class="row row-striped" *ngFor="let booking of bookings">
  <div class="col-2 text-right">
    <h1 class="display-4"><span class="badge badge-secondary">{{ booking.getDateAsDate() | date:'dd'}}</span></h1>
    <h2>{{ booking.getDateAsDate() | date:'MMM' }}</h2>
  </div>
  <div class="col-10">
    <h3 class="text-uppercase"><strong>{{ booking.title }}</strong></h3>
    <ul class="list-inline">
      <li class="list-inline-item">
        <i class="fa fa-calendar-o" aria-hidden="true"></i>{{ booking.getDateAsDate() | date:'EEE'}}</li>
      <li class="list-inline-item">
        <i class="fa fa-clock-o" aria-hidden="true"></i>{{ booking.startTime }} - {{ booking.endTime }}</li>
      <li class="list-inline-item"><i class="fa fa-location-arrow" aria-hidden="true"></i>{{ booking.room.name }}</li>
    </ul>
    <div class="row">
      <div class="col-6">
        <p class="mb-0">Booked by : {{ booking.user.name}}</p>
        <p class="mb-0">Room layout: {{ booking.layout }}</p>
        <p>Participants: {{ booking.participants }}</p>
      </div>
      <div class="col-6">
        <a class="btn btn-warning">Amend</a>
        <a class="btn btn-danger">Cancel</a>
      </div>
    </div>
  </div>
</div>
```

## 134 Exercise 5 - Create the add, edit and delete functionality

Create the editing functionality:

- Create an EditBooking component and set up navigation
- Create the add and edit booking functionality
- Create the delete functionality

## 135 Exercise 5 - Solution walkthrough part 1

First we create the new component: `ng g c calendar/BookingEdit`. The first thing we’re going to do in the TypeScript file is put in a local variable of type Booking. We don’t know at this stage if we’ll need the Input decorator. Because we’re going to be navigating to this object we think we’ll just leave it like that for now.

We add prepared HTML and we’ll work our way through and bind to the object in the backend. When we worked with users we needed to take a copy of the object. We don’t think we’ll need to do that here, because we’re not going to be showing this form at the same time as the view form. So we can just bind straight to that booking object.

### in booking-edit.component.html

```html
<form>
  <div class="row">
    <div class="form-group col-4">
      <label for="date">Date</label>
      <input type="date" class="form-control" id="date" 
												[(ngModel)]="booking.date" name="date">
    </div>
    <div class="form-group col-4">
      <label for="startTime">Start time</label>
      <input type="time" class="form-control" id="startTime" 
										[(ngModel)]="booking.startTime" name="startTime">
    </div>
    <div class="form-group col-4">
      <label for="endTime">End time</label>
      <input type="time" class="form-control" id="endTime" 
										[(ngModel)]="booking.endTime" name="endTime">
    </div>
  </div>
	...
</form>
```

For room we have drop-down *select* element. We want to repeat the room object for each *option* element in this *select*. So we need to get a list of rooms in the backing file.

### in booking-edit.component.ts

```tsx
rooms: Array<Room>;

constructor(private dataService: DataService) { }

ngOnInit(): void {
  this.dataService.getRooms().subscribe(
    next => this.rooms = next
  )
}
```

In the HTML we want to repeat the *option* element for every room, where we set name of every room and we bind the value of every option: `[ngValue]="room"` . And we bind the *select* element to the actual Booking room: `[(ngModel)]="booking.room"`.

### in booking-edit.component.html

```html
<div class="form-group col-5">
  <label for="room">Room</label>
  <select class="form-control" id="room" 
					[(ngModel)]="booking.room" name="room">
    <option *ngFor="let room of rooms" 
						[ngValue]="room" >{{ room.name }}</option>
  </select>
</div>
```

We do the same for layouts and other input elements.

### in booking-edit.component.html

```html
<div class="row">
  <div class="form-group col-5">
    <label for="layout">Layout</label>
    <select class="form-control" id="layout" 
											[(ngModel)]="booking.layout" name="layout">
      <option *ngFor="let layout of layouts" 
											[ngValue]="layoutMap.get(layout)"
      >{{ layoutMap.get(layout) }}</option>
    </select>
  </div>
  <div class="form-group col-2">
    <label for="participants">Participants</label>
    <input type="number" class="form-control" id="participants"
           [(ngModel)]="booking.participants" name="participants">
  </div>
</div>
<div class="row">
  <div class="form-group col-6">
    <label for="layout">User</label>
    <select class="form-control" id="user" 
												[(ngModel)]="booking.user" name="user">
      <option *ngFor="let user of users" [ngValue]="user"
      >{{ user.name }}</option>
    </select>
  </div>
  <div class="form-group col-6">
    <label for="title">Meeting title</label>
    <input type="text" class="form-control" id="title" 
												[(ngModel)]="booking.title" name="title">
  </div>
</div>
```

In order for us to see this working, we need to create some kind of navigation to be able to see this screen. Let’s go to the app module where we’ve defined our navigation and add new routing for edit booking: 

`{path : 'bookingEdit', component : BookingEditComponent},`.

### in calendar.component.ts

```tsx
editBooking(bookingId: number) {
  this.router.navigate(['bookingEdit'], 
						{queryParams: {id: bookingId}});
}
```

### in calendar.component.html

```html
<a class="btn btn-warning" (click)="editBooking(booking.id)">Amend</a>
```

### in booking-edit.component.ts

```tsx
ngOnInit(): void {
    const id = this.route.snapshot.queryParams['id'];
    const idNumber = +id;
    this.dataService.getBookingById(idNumber)
										.subscribe(next => this.booking = next);
    //...
  }
```

We’ve populated the form now. 

## 136 Exercise 5 - Solution walkthrough part 2

The next step is going to be to implement the save button.

### in data.service.ts

```tsx
saveBooking(booking: Booking): Observable<Booking> {
  const existing: Booking = this.bookings
																.find(b => b.id === booking.id);
  booking.copyTo(existing);
  return of(existing);
}
```

### in booking-edit.component.ts

```tsx
saveBooking(): void {
  this.dataService.saveBooking(this.booking).subscribe(
    next => this.router.navigate([''])
  );
}
```

### in booking-edit.component.html

```html
<button type="button" class="btn btn-primary"
        (click)="saveBooking()"
>Save</button>
```

The final step is to add a booking. We’ll create a new URL for the add booking object. We add a new route to app.module.ts:

 `{path : 'bookingAdd', component : BookingEditComponent}`.

### in calendar.component.ts

```tsx
addBooking() {
  this.router.navigate(['bookingAdd']);
}
```

### in booking-edit.component.ts

```tsx
ngOnInit(): void {
  const id = this.route.snapshot.queryParams['id'];
  if (id) {
    const idNumber = +id;
    this.dataService.getBookingById(idNumber)
										.subscribe(next => this.booking = next);
  } else {
    this.booking = new Booking();
  }
	// ...
}
```

### in data.service.ts

```tsx
addBooking(newBooking: Booking): Observable<Booking> {
  let maxId = 0;
  for (const b of this.bookings) {
    if (b.id > maxId) {
      maxId = b.id;
    }
  }
  newBooking.id = maxId + 1;
  this.bookings.push(newBooking);
  return of(newBooking);
}
```

### in booking-edit.component.ts

```tsx
onSubmit(): void {
  if (this.booking.isNew()) {
    this.dataService.addBooking(this.booking).subscribe(
      next => this.router.navigate([''])
    )
  } else {
    this.dataService.saveBooking(this.booking).subscribe(
      next => this.router.navigate([''])
    );
  }
}
```

Final job is the cancel button.

### in data.service.ts

```tsx
deleteBooking(bookingId: number): Observable<any> {
  const found: Booking = this.bookings
															.find(b => b.id === bookingId);
  this.bookings.splice(this.bookings.indexOf(found), 1);
  return of(null);
}
```

### in calendar.component.ts

```tsx
deleteBooking(bookingId: number) {
  this.dataService.deleteBooking(bookingId).subscribe();
}
```

### in calendar.component.html

```html
<div class="col-6">
  <a class="btn btn-warning" (click)="editBooking(booking.id)"
	>Amend</a>
  <a class="btn btn-danger" **(click)="deleteBooking(booking.id)"**
	>Cancel</a>
</div>
```

## 137 Responding to the calendar click event

We want to bind the value of the calendar input element to a date. We add a variable selectedDate: `selectedDate: string;`

### in data.service.ts

```tsx
getBookings(date: string): Observable<Array<Booking>> {
  return of(this.bookings.filter(b => b.date === date));
}
```

### in calendar.component.ts

```tsx
ngOnInit(): void {
  this.route.queryParams.subscribe(
    params => {
      this.selectedDate = params['date'];
      if (!this.selectedDate) {
        this.selectedDate = formatDate(new Date(), 'yyy-MM-dd', 'en');
      }
      this.dataService.getBookings(this.selectedDate).subscribe(
        next =>  this.bookings = next
      )
    }
  )
}
//...
dateChanged() {
  this.router.navigate([''], 
											{queryParams: {date : this.selectedDate}});
}
```

### in calendar.component.html

```html
<h2>The following meetings are taking place on:
  <input type="date" [(ngModel)]="selectedDate" name="selectedDate" 
				(change)="dateChanged()" />
</h2>
```

We’re finally ready to start connecting to a REST backend.