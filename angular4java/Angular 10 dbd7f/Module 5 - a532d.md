# Module 5 - Security and Deployment (part2)

# Chapter 37 - Implementing JWT Authentification in Angular

## 199 - Storing the JWT received from the server

We’ve now secured our REST API so that some of the URLs require a JWT to provide access. In this chapter we will be amending the Angular project to store the JWT that we get back when a user logs in, to send that JWT as part of the headers to access the secured URLs, and to deal with the HTTP status 403, the forbidden response, that we get back if the user doesn’t have the required role to access the feature that they’re trying to access.

Part of what we’re going to do in this chapter, the way that we’re going to store and then send back to the server the JWT, we are going to improve and replace in the next chapter with a slightly more secure way of storing the JWT. But we think it is worth going through this process first, so that we fully understand how everything fits together.

We’d like to start with the call to the *validate* method in the backend. Which is this validate user method in the data service. This method is being called from the auth service.

### in auth.service.ts

```tsx
authenticate(name: string, password: string) {
  this.**dataService.validateUser(name, password)**.subscribe(
    next => {
      this.isAuthenticated = true;
      this.authenticationResultEvent.emit(true);
    },
    error => {
      this.isAuthenticated = false;
      this.authenticationResultEvent.emit(false);
    }
  )
}
```

This value of *next* is going to be the token that’s received back from the server. We want to store that token somewhere and we think here in the auth service is as good as place as any. We create a variable *jwtToken*.

We think it would be sensible in our data service change the return type of the *validateUser* method from being a string to being a JavaScript object with a key of “result”, which will be a string.

### in data.service.ts

```tsx
validateUser(name: string, 
						password: string) : Observable<**{result: string}**> {
  const authData = btoa(`${name}:${password}`);
  const headers = new HttpHeaders().append('Authorization', 'Basic ' 
																						+ authData);
  return this.http.get<**{result: string}**>(
									environment.restUrl + '/api/basicAuth/validate', 
									{headers: headers}
	);
}
```

It means that in our auth service we’ll be able to reference `next.result`

### in auth.service.ts

```tsx
**jwtToken: string;**

authenticate(name: string, password: string) {
  this.dataService.validateUser(name, password).subscribe(
    next => {
			**this.jwtToken = next.result;**
      this.isAuthenticated = true;
      this.authenticationResultEvent.emit(true);
    },
    //...
  )
}
```

We should replicate the change in the local version of the data service.

### in data.service.local.ts

```tsx
validateUser(name: string, 
						 password: string) : Observable<{result: string}> {
  return of({result: "ok"});
}
```

That should now be storing the JWT in the auth service.

## 200 - Sending the Bearer Authorization header

## 201 - Dealing with the 403 response code

## 202 - Finding out the user’s role

# Chapter 38 - Surviving a browser refresh

## 203 - The risks of storing a JWT

In this chapter we’re going to deal with an issue that we have right now with the Angular application that making the way we deal with JWTs more secure is going to turn out to be the fix for. The issue is, that if we refresh the browser window, all of the data about the current logged in user is lost. The reason for that is because the value of *isAuthenticated* in the auth service has been reset to false, and the token has been lost as well. These are stored in memory in JavaScript, but if we refresh the entire application is reloaded from the server and all of that local data is lost.

What we’ll need to do is to store the value of our JWT in a more persistent way. Browsers have a feature called *LocalStorage* and you can, in theory, use LocalStorage to persist the JWT. You can read and write to LocalStorage from JavaScript, but this is a really bad idea. It’s not secure, and the same security issue exists right now in our application. The fact that we’re storing the JWT in JavaScript. We’d like to start by looking at what this issue is and understanding how to fix it.

The issue is a security issue right now because the JWT is stored in memory, and the same would apply if we’d use LocalStorage, we can access it from JavaScript. If we know the JWT, we can send commands to the server as an authenticated user. It is almost as risky as knowing the user’s username and password. The only protection knowing a JWT has over knowing user’s username and password is that the JWT has a limited lifespan. It will expire after a short while. But that could be long enough to do lots of damage. So there’s is a possibility that if someone could inject some malicious code into our application, they can somehow extract the JWT and then use it for their advantage. This risk is called Cross-Site Scripting or XSS for short. The idea is that some JavaScript somehow gets injected into the page, and if this happens, it will have access to any variables that our code itself has access as well.

To protect the JWT we want to store it in such a way that even the JavaScript running in our browser can’t see it. And the solution is that we’re going to change the process so the JWT here is not held in memory within the application, but instead, it’s stored as a cookie. The way that this will work is as follows.

When we do the basic authentication step, the server won’t just send back the JWT anymore as part of the response. Instead, it’s going to send back a response probably with the word “ok”, but with the JWT attached as a cookie. This cookie can be set up in such a way that the JavaScript running in our browser is not able to see it. That’s called an **HttpOnly** cookie. This cookie will then get sent by the browser to the server automatically with any REST call that we subsequently make to the server. So although our JavaScript can’t access the cookie or find out its contents it will still get sent to the server automatically. That means that if we do get any malicious JavaScript code injected into our site, it won’t be able to find out what the JWT is. And because this cookie won’t get destroyed if the user refreshes their browser, it will still be available to be sent by the browser with every REST request. Which means if the user refreshes the browser, the server will still know who our user is. The user will still be authenticated.

## 204 - Setting up a JWT Cookie

## 205 - Sending a cookie from Angular

## 206 - Using the cookie for authentication

## 207 - Securing the cookie and getting the user’s role

## 208 - Surviving the refresh

## 209 - Using the XMLHttpRequest header

## 210 - Exercise 1 - Finishing the Application

## 211 - Exercise 1 - Solution walkthrough part 1

## 212 - Exercise 1 - Solution walkthrough part 2

# Chapter 39 - Building and Deploying

## 213 - Compiling an Angular project

An Angular application consists of an index.html file and some JavaScript files. And some additional resources such as CSS files or images. An Angular project need to be compiled to the JavaScript files ready to be placed on a server.

To start building process run `ng build`. We can optionally include command line arguments to specify the environment we want to use. In our application we can choose to run it in development mode, in production mode, or in local mode. The same command line arguments apply for the *ng build* command. If we run *ng build* with no command line arguments then the application would be built in the configuration for our *development* mode. That is it would be looking for localhost at the backend server. If we want it to be built in local mode we could do `ng build -c local` or for production mode `ng build -c production` or using shortcut `ng build --prod`.

After the building process finishes we have an additional folder in our project called *dist* which is short for distribution. It contains files that are needed to deploy our application to the server.

## 214 - A note about using Git with Angular

The default gitignore file includes *dist* folder. If you’ve setup a continuous integration pipeline, so that for example committing code to GitHub will result in that code being deployed to a production server somewhere, then you may want to include the *dist* folder into your source code distribution, that is you might want to remove it from the gitignore file.

## 215 - Deploying an Angular project

There is one critical step, which is that you do need to configure the web server so that every URL is actually rooted through the `index.html` file. If we were to visit one of the pages within our application with some url which doesn’t exist in the file structure on our server, we want to be sure that no matter what the URL is, it’s always the `index.html` file which is going to be served.

A sample nginx.conf configuration file:

```
events {
  worker_connections  4096;  ## Default: 1024
}

http {
   map $http_upgrade $connection_upgrade {
       default upgrade;
      '' close;
   }

   include /etc/nginx/mime.types;

   server {
      listen 80;

      location / {
         root /usr/share/nginx/html;
         try_files $uri $uri/ /index.html;
      }
    }
}
```

A sample Docker file which will allow you to build a docker image using nginx as the server to create a container that will serve our application:

```
FROM nginx:1.14.0-alpine

MAINTAINER Your name "youremail@server.com"

RUN apk add --update bash && rm -rf /var/cache/apk/*

RUN rm -rf /usr/share/nginx/html/*

COPY /dist/YOUR_PROJECT_NAME/ /usr/share/nginx/html

COPY nginx.conf /etc/nginx/

CMD ["nginx", "-g", "daemon off;"]
```

All you need to do if you are using Docker is copy the Dockerfile and the nginx.conf file to the root of your application. That’s the folder above the *dist* folder. You will need to change the name of your project so it knows which folder contains the code. Then you should be able to use these two files to build a docker image to serve your Angular application.

There is lots of info about deploying an Angular application to be served directly from Amazon’s s3 simple storage service.

Be aware that if you want to use SSL, then you actually need to also use Cloudfront to provide the SSL functionality. You won’t get it if you just use s3 by itself.