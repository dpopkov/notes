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
<div class="form-group" *ngIf="user.id == null">
  <label for="password">Password</label>
  <input type="password" class="form-control" id="password" 
				placeholder="password"
        [(ngModel)]="password" name="password">
  <div class="alert alert-danger">error message placeholder</div>
  <label for="password2">Repeat password</label>
  <input type="password" class="form-control" id="password2" 
				 placeholder="password"
         [(ngModel)]="passwordDouble" name="password2">
  <div class="alert alert-danger">error message placeholder</div>
</div>
```

Let’s start with the first peace of validation which is going to be on the name field. Because we’re working at HTML here and we want this value to be filled in the simplest thing we could do is to particularly require that on the HTML element. We just add the attribute *required*. This is a standard HTML attribute. It’s nothing to do with Angular. And if we go and try this now then Angular will allow to do that. It looks like this *required* element doesn’t work. Actually it is not that it doesn’t work. It does something much more clever in the background. Angular uses dynamic styles in this situation: `ng-valid` or `ng-invalid` - to indicate whether the entry in the form passes the validation rules, `ng-touched` or `ng-untouched` - to indicate whether the user has selected the element either with the mouse or has tabbed into it, `ng-pristine` or `ng-dirty` - to indicate whether the user has edited the value in the control. Let’s adjust our CSS for the component to make the input box have a red border if it’s not valid.

### user-edit.component.css

```css
input.ng-invalid.ng-touched {
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
				 #nameField="ngModel">
  <div *ngIf="nameField.invalid && nameField.touched"
       class="alert alert-danger">Name is required</div>
</div>
```

Ideally we don’t want to be able to submit this form if the form isn’t valid. It would be sensible if we disable the *save* button if the form isn’t valid. We will use template reference for the form to check whether the *save* button should be disabled.

### in user-edit.component.html

```html
<form #editUserForm="ngForm">
	...
  <button type="button" class="btn btn-primary" (click)="onSubmit()"
          [disabled]="editUserForm.invalid"
  >Save</button>
</form>
```

That is the basic validation of the name field set up and working.

## 119 Custom validation rules (5m)

Let’s do something a bit more complicated now, some custom validation. Ideally we want the name to contain more than just spaces. Now a name containing only spaces looks like a valid name, the *save* button is enabled, and we haven’t got any kinds of red hints, and if we click on *save* then the name will be changed to a blank name. We want to have some kind of custom validation that doesn’t allow the name to be blank. We’ll start by creating a variable called *nameIsValid*. Next we will create a function to determine whether or not the name is valid.

### in user-edit.component.ts

```tsx
nameIsValid = false;
// ..
ngOnInit(): void {
  this.formUser = Object.assign({}, this.user);
  this.checkIfNameIsValid();
}
// ..
checkIfNameIsValid(): void {
  this.nameIsValid = this.formUser.name.trim().length > 0;
}
```

### in user-edit.component.html

```html
<div class="form-group">
  <label for="name">Name</label>
  <input type="text" class="form-control" id="name" 
				 placeholder="user name"
         **(change)="checkIfNameIsValid()"**
         [(ngModel)]="formUser.name" name="name" 
				 required #nameField="ngModel">
  <div *ngIf="nameField.invalid && nameField.touched"
       class="alert alert-danger">Name is required</div>
  <div *ngIf="nameField.touched && **!nameIsValid**"
       class="alert alert-danger">Name cannot be blank</div>
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
         (change)="checkIfPasswordIsValid(); checkIfPasswordsMatch()"
        [(ngModel)]="password" name="password" required 
				#passwordField="ngModel">
  <div *ngIf="passwordField.invalid && passwordField.touched"
       class="alert alert-danger">Password is required</div>
  <div *ngIf="passwordField.touched && !passwordIsValid"
       class="alert alert-danger">Password cannot be blank</div>

  <label for="password2">Repeat password</label>
  <input type="password" class="form-control" id="password2" 
				 placeholder="password"
         (change)="checkIfPasswordsMatch()"
         [(ngModel)]="passwordDouble" name="password2" required 
				 #password2Field="ngModel">
  <div *ngIf="password2Field.invalid && password2Field.touched"
       class="alert alert-danger">Re-typed password is required</div>
  <div *ngIf="password2Field.touched && !passwordsMatch"
       class="alert alert-danger">Passwords do not match</div>
</div>

<button type="button" class="btn btn-primary" (click)="onSubmit()"
        [disabled]="editUserForm.invalid || !nameIsValid 
					|| (user.isNew() && (!passwordIsValid || !passwordsMatch))"
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
<a class="btn btn-small btn-warning" (click)="editRoom()">edit</a>
```

Most of the work then to do in navigation is going to work in the rooms component. In the TypeScript file, we don’t have the action there as a string yet. Let’s add that first. Then in the *ngOnInit()* method subscribe on the queryParams, we’re getting the ID, and we’ll also want to set the action at this point. If the action is “add” then we’ll want to set up a new room to work with the edit screen.

### in rooms.component.ts

```tsx
action: string;

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
      if (this.action === 'add') {
        this.selectedRoom = new Room();
        this.action = 'edit';
      }
    }
  )
}
addRoom(): void {
  this.router.navigate(['admin', 'rooms'], 
												{queryParams : { action: 'add'}});
}
```

In the rooms.component.html we’ll start with *add* button. Then we add *app-room-edit* component.

### in rooms.component.html

```html
<a class="btn btn-warning mb-3" (click)="addRoom()">add</a>
...
<div class="col-6">
  <app-room-detail *ngIf="action === 'view'" [room]="selectedRoom">
	</app-room-detail>
  <app-room-edit *ngIf="action === 'edit'" [room]="selectedRoom">
	</app-room-edit>
</div>
```

That’s navigation all set up.

## 124 Creating a reactive form (10m)

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
roomName = new FormControl('roomName');
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
roomForm = new FormGroup(
  {
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

## 125 Setting and reading form control values (4m)

## 125 Programmatically adding controls (11m)