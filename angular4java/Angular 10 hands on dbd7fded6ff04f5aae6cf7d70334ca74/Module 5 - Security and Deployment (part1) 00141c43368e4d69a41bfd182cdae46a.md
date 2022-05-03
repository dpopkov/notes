# Module 5 - Security and Deployment (part1)

# Chapter 34 - Login and Route Guards (23m)

## 178 Introduction to security (3m)

Before we start this module, we’d like to remove the delays that we added into the Spring REST methods as that was only done for testing purposes.

To make our application secure we’re going to need to do a few different things.

- Requiring users to login (login form so that users will need to enter a username and password)
- Authenticating users (via REST)
- Preventing access to unauthorized users (we might create roles to access different features)
- Secure the REST API (so it can only be accessed by authenticated and authorized users)
- All of this is being code in as safe way as possible using production standard techniques

## 179 An overview of the login functionality (2m)

In this chapter we’re going to require our users to provide a login before they can access the application. The home screen of our application is the Calendar component. We’re going to make it so that anybody on the Internet can view this component, you don’t need to be logged in or register to view it. But if you want to be able to access any other component whether that’s to view or edit rooms and users, or to amend a booking, you will need to be logged in. Right now if the user clicks on the link to view the rooms for example, the application will display the Rooms component.

First of all we’re going to create a *AuthService* service. This is a standard service which will store information about the logged in user. May be their name, whether they’re authenticated or not and what role they have.

We’ll also have a Login component which will display a login form. When the user clicks to view the rooms our code can first of all, check the AuthService to see has our user already been authenticated. If they have, our application can display the Rooms component. If our user is not logged in however, then the application will display the Login component.

## 180 Implementing the AuthService and Login component (4m)

Generate service: `ng g s Auth`

### in auth.service.ts

```tsx
export class AuthService {

  isAuthenticated = false;

  authenticate(name: string, password: string): boolean {
    if (name === 'james' && password === '123') {  // for now
      this.isAuthenticated = true;
    }
    return this.isAuthenticated;
  }
}
```

Generate Login component: `ng g c Login`

### in login.component.ts

```tsx
export class LoginComponent implements OnInit {
  message = '';
  name: string;
  password: string;

  constructor(private authService: AuthService) { }

  ngOnInit(): void {
  }

  onSubmit(): void {
    if (this.authService.authenticate(this.name, this.password)) {
      // navigation
    } else {
      this.message = 
					'Your username or password was not recognised - try again';
    }
  }
}
```

### in login.component.html

```html
<h1>Login</h1>

<div class="bg-warning p-5" *ngIf="message">{{message}}</div>

<form #userForm="ngForm">
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name" 
					placeholder="user name" [(ngModel)]="name" name="name">
  </div>
  <div class="form-group">
    <label for="password">Password</label>
    <input type="password" class="form-control" id="password" 
						placeholder="password" [(ngModel)]="password"
           name="password">
  </div>
  <button type="button" class="btn btn-primary" 
					(click)="onSubmit()">Login</button>
</form>
```

## 181 Implementing a Route Guard (7m)

We’ve built the service and the component. Now we need to think about the redirection pace. The good news is that there is a feature in Angular that will take care of this for us automatically. We’ll use what is called a route guard. A route guard is a way of protecting any of the routes that we define in app module. So if we wanted to display the rooms component, for example, we’ve given that a route to that module, we’re going to say that that is a guarded route. By applying a route guard to a route, it means that there will be a check performed to see if the user is allowed to access this requested route. This access will be determined by looking at the service we need to write called a route guard. So a route guard is a service which implements a specific interface.

The idea is that when the user tries to access the rooms component, Angular will automatically ask the route guard service: “is this allowed?”. The route guard can then check out auth service to allow us to decide if it’s permitted or not. And if it isn’t, it’s the route guard service that will redirect the user to the login component.

We create another service: `ng g s AuthRouteGuard`

You could have many different route guards in our application if you want to be protecting different URLs in different ways. We’re only going to have the one

We’re keeping the route guard and the auth service separate just because we think it will help to understand more easily what’s going on. The route guard is going to be responsible for the navigation part. The auth service is responsible for authentication and storing whether or not our user is authenticated.

### in auth-route-guard.service.ts

```tsx
export class AuthRouteGuardService implements CanActivate{

  constructor(private authService: AuthService) { }

  canActivate(): boolean {
    return this.authService.isAuthenticated;
  }
}
```

### in app.module.ts

```tsx
const routes: Routes = [
  {path : 'admin/users', component : UsersComponent, 
		**canActivate : [AuthRouteGuardService]**},
  {path : 'admin/rooms', component : RoomsComponent, 
		**canActivate : [AuthRouteGuardService]**},
  {path : '', component : CalendarComponent},
  {
    path : 'bookingEdit',
    component : BookingEditComponent,
    resolve : {
      rooms : PrefetchRoomsService,
      users : PrefetchUsersService
    },
    **canActivate : [AuthRouteGuardService]**
  },
  {
    path : 'bookingAdd',
    component : BookingEditComponent,
    resolve : {
      rooms : PrefetchRoomsService,
      users : PrefetchUsersService
    },
    **canActivate : [AuthRouteGuardService]**
  },
  **{path : 'login', component : LoginComponent},**
  {path : '404', component : PageNotFoundComponent},
  {path : '**', redirectTo: '/404'}
];
```

## 182 Redirecting with route guards (6m)

The next step is we actually want, if the RouteGuard doesn’t pass, for our user to be redirected to the Login component. We do that in the RouteGuard itself.

### in auth-route-guard.service.ts

```tsx
export class AuthRouteGuardService implements CanActivate{

  constructor(private authService: AuthService,
              **private router: Router**) { }

  canActivate(**route: ActivatedRouteSnapshot, 
							state: RouterStateSnapshot**): boolean {
    if (!this.authService.isAuthenticated) {
      **this.router.navigate(['login'], 
													{queryParams: {requested: state.url}});**
    }
    return this.authService.isAuthenticated;
  }
}
```

### in login.component.ts

```tsx
export class LoginComponent implements OnInit {
  message = '';
  name: string;
  password: string;

  constructor(private authService: AuthService,
              **private router: Router,
              private activatedRoute: ActivatedRoute**) { }

  ngOnInit(): void {}

  onSubmit(): void {
    if (this.authService.authenticate(this.name, this.password)) {
      **const url = this.activatedRoute.snapshot.queryParams['requested'];
      this.router.navigateByUrl(url);**
    } else {
      this.message = 'Your username or password was not recognised - try again';
    }
  }
}
```

# Chapter 35 - Authenticating users via REST (basic auth) (32m)

## 183 The concept of Basic Authentication (4m)

In this chapter we’re going to use the REST backend to validate the users credentials.

The principle of Basic Authentication is that we will provide one or more REST methods in our backend but these will be secured. We’ll only allow the REST method to respond if the users credentials are included within a header which is sent with the REST call. The credentials have to be supplied in a header in a particular format and that format is a header which looks something like this:

`Authorization: Basic some_string_here` - and the string contains the username and password. The string has a specific format which actually is to take the username, then a colon and then a password, and to encode it using something called Base-64 encoding. Base-64 encoding is not in any way secure, it is just a way of combining data, which need not necessarily be text, into a text string. If you’re going to use this on a live application on the Internet, then it’s important that the call to REST is done over SSL. The URL should start with *https* and you should have a valid security certificate on your server. That way this string can’t be intercepted between the client and the server.

## 184 Implementing Basic Authentication in Spring (10m)

Let’s start in the backend and we’ll create a new validateUser method and implement some basic authentication to secure it. We create a new *ValidateUserController*. All the methods in this controller we’re going to secure using Basic authentication. When we write the security configuration we can say “any URL that starts with `api/basicAuth` must be secured by basic authentication”.

### in ValidateUserController.java

```java
@RestController
@RequestMapping("/api/basicAuth")
public class ValidateUserController {

    @RequestMapping("/validate")
    public String userIsValid() {
        return "{\"result\" : \"ok\"}";
    }
}
```

### in BasicAuthConfig.java

```java
@Configuration
@EnableWebSecurity
public class BasicAuthConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) 
					throws Exception {
        auth.inMemoryAuthentication()
                .withUser("james")
                .password("{noop}123")
                .authorities("ROLE_ADMIN");
        // todo: this password should be encoded
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers("/api/basicAuth/**")
                .hasRole("ADMIN")
                .and()
                .httpBasic();
    }
}
```

## 185 Testing Basic Authentication (2m)

Test using curl: `curl http://localhost:8080/api/basicAuth/validate`

That should returns something like: {"timestamp":"2022-03-27T13:03:58.856+00:00",**"status":401**,"error":"Unauthorized","message":"Unauthorized","path":"/api/basicAuth/validate"}

We’ve base-64 encoded string “james:123” and received “amFtZXM6MTIz” string.

Curl: `curl -H "Authorization: Basic amFtZXM6MTIz" http://localhost:8080/api/basicAuth/validate`

will give `{"result" : "ok"}` response.

## 186 Implementing Basic Authentication in Angular (9m)

We go back to the AuthService. It’s doing authentication locally, but we’ll now want to call the backend in a secure way to do the REST call using basic authentication. We’ll take the name and the password that the user entered as the parameters for the header in our basic authentication. We need an extra method in the data service.

### in data.service.ts

```tsx
validateUser(name: string, password: string) : Observable<string> {
    const authData = btoa(`${name}:${password}`);
    const headers = new HttpHeaders().append('Authorization', 
																							'Basic ' + authData);
    return this.http.get<string>(environment.restUrl 
				+ '/api/basicAuth/validate', {headers: headers});
  }
```

### in data.service.local.ts

```tsx
validateUser(name: string, password: string) : Observable<string> {
  return of("ok");
}
```

### in auth.service.ts

```tsx
export class AuthService {

  isAuthenticated = false;
  **authenticationResultEvent = new EventEmitter<boolean>();**

  constructor(**private dataService: DataService**) {
  }

  authenticate(name: string, password: string) {
    **this.dataService.validateUser(name, password).subscribe(
      next => {
        this.isAuthenticated = true;
        this.authenticationResultEvent.emit(true);
      },
      error => {
        this.isAuthenticated = false;
        this.authenticationResultEvent.emit(false);
      }
    )**
  }
}
```

### in login.component.ts

```tsx
export class LoginComponent implements OnInit, OnDestroy {

  message = '';
  name: string;
  password: string;
  **subscription: Subscription;**

  constructor(private authService: AuthService,
              private router: Router,
              private activatedRoute: ActivatedRoute) { }

  ngOnInit(): void {
    **this.subscription = this.authService.authenticationResultEvent
																				.subscribe(
      result => {
        if (result) {
          const url = this.activatedRoute.snapshot.queryParams['requested'];
          this.router.navigateByUrl(url);
        } else {
          this.message = 'Your username or password was not recognised - try again';
        }
      }
    )**
  }

  onSubmit(): void {
    **this.authService.authenticate(this.name, this.password);**
  }

  **ngOnDestroy(): void {
    this.subscription.unsubscribe();
  }**
}
```

## 187 Configuring CORS for Basic Authentication (7m)

We’ve changed the way this works quite significantly now. Let’s just talk through it before we go and test it. When we load up the login components, the login screen, at the point of loading it up we go to set up subscription to listen out for authentication results. Every time we click on submit, we go into the authentication service to say “Try and authenticate this user please”. That’s going to make a REST call. When the REST call comes back, it’s going to send off one of these events, which we’re then subscribing to, and that’s when we’ll get our results.

# Chapter 36 - Securing the REST API with JWT (1h9m)

## 188 Why JWT are a good idea

We have so far dealt with authenticating users to protect the Angular application. We can stop users reaching parts of the Angular application that we don’t want them to be able to reach without first logging on. But at the moment, our REST interface is completely open. Anyone who can get hold of one of the URLs can run these URLs directly, including posting data to those POST and PUT URLs, so we now need to start securing these.

In theory, you could do this using basic authentication. We could make the entire REST API protected in the same way that we protected the *validate* URL, but this is not an ideal way of doing things. Mainly because for this to work we’ll need to store the username and password that the user enters through the frontend in Angular in memory. And then every time we click on something, we’ll be able to retrieve that data from the memory to send it in the headers to our REST API. That’s not ideal, and there’s an alternative approach, which is a better way to achieve this, and that is to use a technology called JSON Web Tokens or JWT.

As a starting point, the idea is that we’ll first use the basic authentication that we already set up to validate the user. So we’ll be passing across the user’s username and password to allow us to check this is a valid user. But instead of the server sending back to our client, just the string saying “everything is ok”, instead it’s going to send back a much longer stream known as a token. This token will identify who the user is. It can contain information about the user, such as what roles they have, and importantly, it’s going to be digitally signed by the server. The idea is then is that every time the client wants to make a future REST request to the server it will send that token in the headers to the server with each REST request. And the server can use the token to quickly and easily validate the user’s information. Instead of using basic authentication, this way of authenticating with a token is known as **Bearer authentication**. 

The main advantages of using JWT over basic authentication:

- No need to store username and password on client (we’ll need to store the token rather than the user’s credentials)
- Server can authenticate & authorize completely in memory in a stateless way(without accessing database), it can get everything it needs from the token.
- Tokens can expire (it is possible to include timeout within the token), after that time a new token will be required.

The basic authentication must take place over SSL, and even then if the REST request is somehow intercepted, then the hacker would get the user’s credentials. However if he is able to access a JWT, all the gets is a token, which has a limited lifetime. So the ability to do damage is going to be very limited.

## 189 What are JWTs

A typical JSON Web Token is a long string. It is made up of three parts, each separated by a full stop, or a period symbol. The 1st part is a base64 encoded JSON string. Base64 is not secure, it can be easily decoded. This 1st part is known as the header of the token.

```json
{"alg":"RS256",
"typ":"JWT"}
```

Where *typ* is the token type and that will always be JWT, *alg* is the algorithm that has been used to encode the signature of the token.

The 2nd part is again a base64 encoded another JSON encoded statement containing information about the user. This section is know as the payload of the token. It’s any data that we decide as the programmers we want to include in the token. The point of the payload is that we could put anything we like into it. Generally we will include information that identifies the user and any other information that we want the server to know about the user every time it receives a REST request.

```json
{"user_id":123,
"role":"user",
"expires":1516239022}
```

Nothing we have seen so far is secure. If somebody get this token it’s trivial to do the decoding and find out the information in this payload section. What’s really important is that you don’t put anything sensitive in the payload. You definitely do not want to put a user’s password into the token.

The final part of the token is again base64 encoded. In order to generate this 3rd part of the token what the server did is it took the first two parts and it encoded them using an algorithm in the header. Then this encoded data is then base64 encoded to put into the token.

When the server gets this token back in a future REST call, it knows how to decode this section correctly and it can check that tour signature matches. In other words it can check it’s a valid token.

The algorithms in the example is RS256. It’s a very secure way of encrypting data using a private and a public key. All you really need to understand at this point is that it makes it really difficult, if not impossible, for someone to try and create a JWT that our server will accept. In order to create a JWT, they need to get the signature correct, and they’ll only be able to do that if they can get the secret key that only the server knows. And this makes our JWT really secure. It’s not that the data it contains is secure. We’ve seen that the payload, the data it contains, can be accessed and read by anyone who has this key. What makes it secure is that this signature part means it’s really hard, if not impossible, to fake a JWT. If it’s not our server that’s issued the token, it wouldn’t recognize the token. And because tokens can expire after a short while automatically, even if the token does get intercepted it won’t last very long anyway.

So this process allows the workflow to take place. The user will first be authenticated with the server using basic authentication. The server will then generate the token. That will include the payload data, who the user is, what role they have, and a a signature, which it generates with it’s public key and it will return this token to the client. The client can then on any future REST request send that token back to the server. When the server receives the token, it can check that the signature matches the other data. If any part of the JWT has been tampered with, then the server will know it’s not valid. That’s secure then, because the server will never give out the public or the private key, and it’s almost impossible for anyone to generate the JWT other than the server. When the server checks that the token is valid, it’s already got all the data it needs about the user. And the server can also check that the key hasn’t expired.

## 190 Preparing Spring Boot for JWTs

We have introduced basic authentication, but there is an extra security risk called CSRF (Cross Site Request Forgery). By the end of this module we won’t have to worry about that because we’ll be using these tokens in a really secure way. So we’re going to disable the CSRF protection on the server.

### in BasicAuthConfig.java

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
            .csrf().disable()
            // ...
}
```

The next thing we’d like to do is because we want this to be a production standard example we’d like to create roles for our users. Right now we’ve defined one user in memory with the role of admin. What we’ll do is create a 2nd user with a different role.

### in BasicAuthConfig.java

```java
@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication()
      .withUser("james").password("{noop}123").authorities("ROLE_ADMIN")
      **.and()
      .withUser("jane").password("{noop}123").authorities("ROLE_USER")**;
}
```

We’re going to be authenticating our users against this in-memory list. That has nothing to do with the list of the users registered in the system that we can see on the users admin screen (the first single user for that list is initialized in *DataInitialization* class).

We need to add an extra dependency into our application.

```xml
<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
		<scope>compile</scope>
    <version>3.19.1</version> <!-- Uses jackson-databind which uses version that contains vulnerabilities -->
</dependency>
```

## 191 Generating a JWT

We’ll create a service both to generate and validate tokens.

### in JwtService.java

```java
@Service
public class JwtService {

    private static final int KEY_SIZE = 2048;
    private static final long EXPIRATION_TIME_MS = 1_800_000;

    private RSAPrivateKey privateKey;
    private RSAPublicKey publicKey;

    @PostConstruct
    private void initKeys() throws NoSuchAlgorithmException {
        KeyPairGenerator generator = KeyPairGenerator.getInstance("RSA");
        generator.initialize(KEY_SIZE);
        KeyPair keyPair = generator.generateKeyPair();
        privateKey = (RSAPrivateKey) keyPair.getPrivate();
        publicKey = (RSAPublicKey) keyPair.getPublic();
    }

    /* If a database with real users is used 
			 then the user ID might be put in here. */
    public String generateToken(String name, String role) {
        return JWT.create()
                .withClaim("user", name)
                .withClaim("role", role)
                .withExpiresAt(new Date(System.currentTimeMillis() 
																				+ EXPIRATION_TIME_MS))
                .sign(Algorithm.RSA256(publicKey, privateKey));
    }
}
```

## 192 Generating the Payload data

### in ValidateUserController.java

```java
@RestController
@RequestMapping("/api/basicAuth")
public class ValidateUserController {

    private final JwtService jwtService;

    public ValidateUserController(JwtService jwtService) {
        this.jwtService = jwtService;
    }

    @RequestMapping("/validate")
    public Map<String, String> userIsValid() {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        // Important: using org.springframework.security.core.userdetails.User
        User currentUser = (User) authentication.getPrincipal();
        String name = currentUser.getUsername();
        // We expect the user to have one role such as "role_admin" or "role_user".
        String role = currentUser.getAuthorities().toArray()[0].toString().substring(5);
        String token = jwtService.generateToken(name, role);
        return Map.of("result", token);
    }
}
```

## 193 Testing the JWT

Encode “username:password” using base64 and send

`curl -H "Authorization: Basic here-base64-encoded-string" http://localhost:8080/api/basicAuth/validate`

The simple call `curl http://localhost:8080/api/basicAuth/validate` or a call with fictional user will get status 401 - “Unauthorized”.

The call with actual user and password but without proper role will get status  403 - “Forbidden”.

The call with actual valid user and password will get status 200 and a JWT as a result.

## 194 Validating a JWT

### in JwtService.java

```java
@Service
public class JwtService {
	// ...
  /** Returns a JSON string containing the payload of the specified JWT. */
  public String validateToken(String token) throws JWTVerificationException {
      String encodedPayload = JWT.require(Algorithm.RSA256(
									publicKey, privateKey))
              .build()
              .verify(token)
              .getPayload();
      return new String(Base64.getDecoder().decode(encodedPayload));
  }
}
```

## 195 Configuring Spring to use bearer authentication part 1

To configure Spring we’ll want to edit our security configuration. The first thing we’ll want to do is set up an authentication mechanism. In the *BasicAuthConfig* class where we did our basic authentication we have this call to *httpBasic()* method. That tells Spring to use basic authentication. It tells Spring to look for an authorization header. That header should say “Basic” as the authorization type and then it can decode the base64 encoded string contained in that header and see if it matches one of our known users. If the user is valid, it creates an *Authentication* object representing that user. We unfortunately don’t have a handy method like this for JWT authentication. So we’re going to create our own.

196 Configuring Spring to use bearer authentication part 2

197 Configuring Spring to use bearer authentication part 3

198 Testing validation of the JWT