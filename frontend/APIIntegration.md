# A Beginner's Guide to APIs: How to Integrate and Use Them
Alex Trost
Developer
Do you want to pull in weather data for your users? Get them the latest sports scores for your app? Want to make a site that tells your user a random joke?

# You could go about writing all those jokes yourself or copying and pasting them into a file for your site to read from. Or you could just start using API integration and give your code superpowers to automate the whole process.

When you learn how to use an API, you're able to use services that would otherwise take you a long time to code yourself. You can add a robust search to your site with Algolia's API or a complete eCommerce experience with a SaaS Snipcart.

In this article, we'll go through:

What’s an API?

How to do an API integration?

How to make a simple app with an API?

How to troubleshoot API issues?

Best API integrations to get started with

No-Code API integration platforms

I'm excited to get you up and running with APIs integration! Before making a demo app with an API, let’s learn...

# What’s an API?
API stands for Application Programming Interface, so we'll start by learning what an interface is.

# What’s an interface?
Every device we use has some kind of interface. Your microwave has numbers and a start button on it, while a light switch has an even more straightforward interface.

We use these interfaces to get the device to do what we want. We don't need to understand the underlying circuitry and science to heat a bean burrito. We only need to use the interface that's been exposed to us.

Compare the internal workings of a car engine to what we interface with.

Comparing the complexity of the motor and the simplicity of the interface (Pedals) to API abstraction)
The inner complexity is abstracted away, leaving the user with the most straightforward interface possible.

APIs provide a layer of abstraction for the user. Abstraction hides everything but relevant to the user, making it simple to use. Abstraction is a concept you'll often see in programming, so it's helpful to understand it well.

What’s the "application programming" in API
Now that we know what the Interface portion means, the Application Programming bit is easier to understand.

# An API is how applications talk to each other.

All software that you can interact with through code has some form of an API, so you'll see the term pop up in many places.

When web developers talk about "hitting an API," they usually mean a web service that lets you send requests and receive data in return. We'll touch on these soon.

Whenever I'm wondering, "How do I get this code to do what I want?" I searched for the API documentation related to that code.

You might have looked at the documentation on JavaScript libraries like Lodash to figure out how you need to format your code. The documentation teaches you how to use the API for that library.

# How do web APIs work?
Your web browser has plenty of APIs built into it that we can use! These are called Web APIs. Chrome, Firefox, Safari, etc., have them built-in to use them to add features to our sites.

Some APIs let you play audio files, let your app understand user speech, respond to video game controllers, and lots more! When you listen for a click or a keyboard press in JavaScript, you're using the Event web API to do it.

We'll mainly look at HTTP web APIs for the rest of this article since web developers are most often referring to them when talking about API.

These are APIs that sit between your code and some data sources or functionality on a server that you'd like to access. They most often use the REST API architectural style to conform to certain criteria when making HTTP requests.

# HTTP Web API Diagram
The API generally does two things:

For one, it sets rules for interacting with it.

The "rules" are the API saying, "if you structure your request like this, I'll send you data that's structured like this." If you don't structure your request in a way the API is expecting, it won't know what you want, and you'll get an error in response.

The API also handles data transfer between the server and the code making the request. The API is a program that acts as a middleman between the web app and the server and database.

Once it receives a valid request, it will run a function (or multiple functions). This is the complexity that the API is abstracting for the user. Depending on what you ask for, it might return an image, some data, or just let you know that it successfully received your request.

Let's touch on some concepts that you should understand when working with HTTP APIs.
```javascript
Endpoints
APIs provide you with an endpoint or a specific URL where the data or functions you want are exposed. For Unsplash's source API, you access images through their endpoint at [<https://source.unsplash.com/>](<https://source.unsplash.com/>), adding your query parameters after the end slash.

In a later section, we'll look at some API documentation that outlines this agreement.
```
# Authentication
Some APIs require you to sign up for an account or obtain a unique key to access their information. It might be to secure data, prevent abuse of the service, or because they want to charge a fee for the data.

If you're changing data on your database through an API, you need authentication. You don't want to give anyone else the ability to edit or delete your files!

With authentication, you pass the API a secret key that identifies a specific user or application request. The server can then determine if you're able to access the data or not.

If an API requires authentication, the API's documentation will explain how that works.

HTTP Verbs
With each HTTP request created, there is always an 'HTTP Verb' that goes along with it. The most commons are GET, POST, PUT, and DELETE.

When websites request data from a server, that's typically a GET request. POST and PUT are used to change or add data and DELETE deletes a specific resource.

This article only looks at public APIs, which usually only allow GET requests. So while we won't be using the other verbs, it's important you know they exist. It's a must-have for many web apps.

# When Building an App
In your time as a developer, you might work for a company creating an application. If you're working as a frontend developer, you'll be provided API endpoints by your backend developers, along with directions for how to make requests and what to expect in return.

If you're working as a backend developer, it's your job to design and implement the APIs that run functions and query the database. You'll want to provide your frontend developers with clear documentation on how the API works.

If you're full-stack or building your own app, you might need to handle both parts. Luckily if you're using services like Auth0 for identity management, the creation of the API is handled for you.

Working with JSON
Most HTTP APIs you use will take and receive data in the JSON (JavaScript Object Notation) format. It makes learning how to read and write this format a pretty essential skill. JSON keeps its data in key-value pairs. Let's look at the JSON we get back when we request data from the Star Wars API. If we request this URL:

<https://swapi.dev/api/people/5/>
We will receive this JSON response:

{
    "name": "Leia Organa",
    "height": "150",
    "mass": "49",
    "hair_color": "brown",
    "skin_color": "light",
    "eye_color": "brown",
    "birth_year": "19BBY",
    "gender": "female",
    "homeworld": "<http://swapi.dev/api/planets/2/>",
    "films": [
        "<http://swapi.dev/api/films/1/>",
        "<http://swapi.dev/api/films/2/>",
        "<http://swapi.dev/api/films/3/>",
        "<http://swapi.dev/api/films/6/>"
    ],
    "species": [],
    "vehicles": [
        "<http://swapi.dev/api/vehicles/30/>"
    ],
    "starships": [],
    "created": "2014-12-10T15:20:09.791000Z",
    "edited": "2014-12-20T21:17:50.315000Z",
    "url": "<http://swapi.dev/api/people/5/>"
}
You can see the key/value relationship here. The key "name" has a value of "Leia Organa". We can use this object in our JavaScript code to display the information we choose or even make follow-up API requests.

If we were to query for https://swapi.dev/api/people/6/, the keys (name, height, mass) would remain the same, but the values (Leia Organa, 150, 49) would change.

JSON is a lightweight format that can be used across JavaScript, Python, PHP, and any other language you might be using on the web.

How to make an API integration?
Now that we have a better understanding of what APIs are, let's look at the integration process of an actual API and make our first requests. We're going to start simple with a joke API.

First, head to this URL.

The entire documentation takes place in this README.md file.

Joke API Docs
