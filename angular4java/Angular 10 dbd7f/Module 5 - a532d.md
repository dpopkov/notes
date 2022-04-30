# Module 5 - Security and Deployment (part2)

# Chapter 37 - Implementing JWT Authentification in Angular

## 199 - Storing the JWT received from the server

We’ve now secured our REST API so that some of the URLs require a JWT to provide access. In this chapter we will be amending the Angular project to store the JWT that we get back when a user logs in, to send that JWT as aprt of the headers to access the secured URLs, and to deal with the HTTP status 403, the forbidden response, that we get back if the user doesn’t have the required role to access the feature that they’re trying to access.

Part of what we’re going to do in this chapter, the way that we’re going to store and then send back to the server the JWT, we are going to improve and replace in the next chapter with a slightly more secure way of storing the JWT. But we think it is worth going through this process first, so that we fully understand how everything fits together.

We’d like to start with the call to the *validate* method in the backend. Which is this validate user method in the data service. This method is being called from the auth service.

## 200 - Sending the Bearer Authorization header

## 201 - Dealing with the 403 response code

## 202 - Finding out the user’s role

# Chapter 38 - Surviving a browser refresh

# Chapter 39 - Building and Deploying