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

144 Testing rest method (3m)

145 Exercise 1 - creating rest methods (2m)

146 Exercise 1 - solution walkthrough (8M)

147 Adjusting the User object

# Chapter 29 - Connecting to a REST endpoint (41m)

# Chapter 30 - Dealing with slow and unavailable connections (26m)