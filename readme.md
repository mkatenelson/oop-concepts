<!--
Creator: Ben Hulan
Market: SF
-->

![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)

# Going Deeper with OOP Concepts and JavaScript

## Why is this important?
<!-- framing the "why" in big-picture/real world examples -->
Understanding OOP concepts gives us an excellent frame of reference for a lot of information that comes later in the course. OOP concepts provide the foundation for MVC architecture, Ruby classes, the structure of Rails apps and so much more. OOP design patterns give us an opinion about the way we architect our own applications. Simply put, understanding Object Oriented Programming will make us better developers.

As we prepare for Project 1 browser games, OOP concepts like *inheritance, encapsulation, polymorphism and abstraction* can inform us as we break down complex problems into multiple smaller, simpler steps.


## What are the objectives?
<!-- specific/measurable goal for students to achieve -->
*After this workshop, developers will be able to:*

- Refactor code with OOP concepts such as
    - inheritance
    - encapsulation
    - abstraction
    - polymorphism
- Solve complex coding challeges by breaking them down into smaller tasks
- Recognize, understand and write prototype functions and reusable helper methods

## Where should we be now?
*Before this workshop, developers should already be able to:*

- Code working solutions for projects of a complexity similar to the [tic-tac-toe](https://github.com/sf-wdi-29/tic-tac-toe) and [geoquakes](https://github.com/sf-wdi-29/geoquakes) labs.
- Explain how basic object inheritance works in JavaScript

## What is OOP?

Object Oriented Programming is one of the programming paradigms that evolved in response to [the software crisis](https://en.wikipedia.org/wiki/Software_crisis) of the late 1960's and early 1970's. (Another paradigm is called [Procedural Programming](http://study.com/academy/lesson/object-oriented-programming-vs-procedural-programming.html).) As computers became increasingly powerful, they were tasked with solving more and more complicated problems. Developers needed a system to organize their code in order to improve quality and work more efficiently.

Here is a clear illustration of the main concepts that define Object Oriented Programming:

<figure>
  <img src="https://atomicobject.com/uploadedImages/archive/images/ObjectModel.png" alt="Object Model">
  <br>
  <figcaption>A little bit overwhelming, right?</figcaption>
</figure>

_Don't worry._ We don't need to absorb all of this today. The goal here is simply exposure, to make sure you recognize these words as OOP concepts and begin to develop a comfort with them. Today, we're going to look at four of these concepts in depth and discuss how they relate (or how they _DON'T_ relate) to JavaScript.

#### Important Object Oriented Programming Concepts:

>***Inheritance:*** objects can inherit features from other objects. 
>***Encapsulation:*** each object is responsible for specific tasks. Manage (minimize) dependencies
>***Abstraction:*** "focuses on the essential characteristics of some object relative to the viewer" (Grady)
>***Polymorphism:*** objects can share the same `interface` with other objects.

#### Inheritance and JavaScript

Of all the OOP concepts we could talk about, *inheritance* is probably the most easily understood in the context of JavaScript. That's because whereas OOP concepts were developed using strongly typed (aka classical) languages like C++, JavaScript was written (in 10 days) in 1995 using prototypal inheritance (instead of classes) and so inheritance happens by default with JavaScript's loosely-typed nature.

The basic idea is that any instance of an object inherits properties from its parent object or constructor.

***Example:*** (This should be familiar.)

```js 
function Flower() {
    this.color = "red";
    this.petals = 32;
    this.smells= true;
 }

var tulip = new Flower();
```

We expect tulip to have the `color` red, 32 `petals` and `smells` to be true.

We can describe the relationship between an object and its prototype by saying `tulip is-a Flower`. This heirarchical description (`is-a`) will become more relevant when we look at database relationships. We might say that a `user has-many posts`, for example.


***A Little Deeper:*** All functions in JS have a `prototype` attribute with the value of `Object` and a reference to its definition as a `constructor` method. Objects in JS do NOT have a `prototype` attribute, but they do have a `__proto__` getter and setter method that points to the base Object from which they all derive.

```js
typeof(Flower);     // expect ==> 'function'
typeof(tulip);        // expect ==> 'object'

Flower.prototype;  // expect ==> 'Object {constructor: function Flower() ...}'
tulip.prototype;     // expect ==> 'undefined'

// NOTE TO INSTRUCTORS:  DON'T SHOW THEM THIS!!!
tulip.__proto__;    //  expect ==> 'Object {constructor: function Flower() ...}'

```

#### Check For Understanding:

<details>
  <summary>Can you prove that JavaScript is Loosely-typed?</summary>
  ```js
    var a = "I am a string";
    typeof(a);    //  expect ==> 'string'

    a = 7;
    typeof(a);    //  expect ==> 'number'

  ```
</details>


#### Encapsulation

The Object Oriented Programming paradigm embraces the concept of encapsulation. An encapsulated object or function is one that is responsbile for a single, specific task. Sandi Metz wrote [a great book about Ruby](http://www.poodr.com/) that explains the value of this concept in excellent detail.

It's important to point out that one very important purpose of encapsulation concerns data privacy, which is a little harder to implement in JavaScript, where privacy is primarily managed by scope. _(More on this in the section on *Abstraction*.)_

That said, it is very popular in JavaScript to use utlility libraries and to write helper methods. These approaches promote code reuse, embracing the OOP concept of *encapsulation*.

Instead of this:
```js
    $(document).on("ready", function() { 
        $quakesList = $.ajax({
            method: "GET",
            url: weekly_quakes_endpoint
        })
        .done(function(data){

           var earthquakes = data.features;

            var source = $("#quakes-template").html();
            template = Handlebars.compile(source);

            var quakesTemplate = template({quakes: earthquakes});
           
            $("#info").append(quakesTemplate);

            earthquakes.forEach(function(quake){
                var tempLat = quake.geometry.coordinates[1];
                var tempLng = quake.geometry.coordinates[0];
                new google.maps.Marker({
                    position: new google.maps.LatLng(tempLat, tempLng),
                    map: map,
                    title: quake.properties.title
                });
                
            })

        });
    });
```

Consider this:
```js

    $(document).on("ready", function() {        
        $quakesList = $.ajax({
            method: "GET",
            url: weekly_quakes_endpoint
        })
        .done(function(data){
            onSuccess(data);
        })
        .fail(function(response){
            console.log("Error: ", response);
        });
    });

    function onSuccess(data){
       var earthquakes = data.features;
       createMarkers(earthquakes);
       compileHandlebarsTemplate(earthquakes, "#info", "#quakes-template");
    }

    function createMarkers(locationArray){
        locationArray.forEach(function(location){
            var tempLat = location.geometry.coordinates[1];
            var tempLng = location.geometry.coordinates[0];
            new google.maps.Marker({
                position: new google.maps.LatLng(tempLat, tempLng),
                map: map,
                title: location.properties.title
            });
            
        })
    }

    function compileHandlebarsTemplate(data, targetHtml, targetScript){
        var source = $(targetScript).html();
        template = Handlebars.compile(source);

        var dataTemplate = template({quakes: data});

        $(targetHtml).append(dataTemplate);
    }

```

It still needs work, but you can see that if we had another data set we could createMarkers and compile the data to another Handlebars template with just a few additional adjustments.

#### ¿Comprendes Mendez?

<details>
  <summary>Can you suggest ways to further encapsulate this code?</summary>
    - Replace `var earthquakes` in the `onSuccess()` method with a more generic variable name
    - Figure out how to determine whether Latitude and Longitude are at index [1] or [0] in a given dataset
    - Create a more generic property name for the Handlebars template and make sure that is used in the in-line Javascript
    - More ideas...?
</details>


#### Abstraction

OOP Abstraction is specifically talking about classes, which we don't really have in ES5 JavaScript. In classical languages, as I've mentioned, you typically define a class with private and public methods and variables.

The basic idea of encapsulated data privacy is that some objects should have access to your data while others should not. In classically-typed languages, you often see public and private methods, or interface code separated from implementation code. The goal is to encapsulate or hide the details of implementation so your public class methods are more behavior driven.

PRIVATE | PUBLIC 
----- |---- 
Data | read/write access
Private Functions | ADT Functions
   | Constructors / Destructors

_That's already more than you really need to know about abstraction, at this point._

#### The relationship between Encapsulation and Abstraction

<details>
  <summary>What does ADT stand for, and why should we care?</summary>
    ADT stands for Abstract Data Types. ADT functions are the specific functions we write in a class that give us its unique purpose for existence.
</details>


#### Polymorphism

The word Polymorphism describes the ability for OOP objects to take different shapes. A little less obvious is the fact that OOP objects should be modular. Later in this cohort we will import the same resource into multiple JavaScript files and use that resource for different purposes. For example, we might have user.posts and user.comments. Both need to import the core User model but they will endow the user with different behaviors.

Here's a more literal example to _illustrate_ polymorphism, except it mainly _demonstrates_ inheritance.


```js
function Polygon() {
    this.width = b;
    this.height = h;
    this.hasArea = true;
 }

var rectangle = new Polygon();
rectangle.sides = 4;
rectangle.area = b * h;

var triangle = new Polygon();
triangle.sides = 3;
triangle.area = b * h / 2;


```

A rectangle and a triangle are both polygons but they are not identical.

 
### Check for Understanding

<details>
  <summary>Brainstorm another example of how you might use polymorphism in JavaScript.</summary>
  - Objects should be modular
  - Objects with different implementations can share the same interface
  - There is some conceptual similarity between inheritance and polymorphism
</details>

## Practice

>***Note:*** *This can be a pair programming activity or done independently.*

- Pick a strong solution from either the [geoquakes](#) or [tic-tac-toe]() mini-projects.
- Identify the main object from which all the others are derived
- Apply as many OOP concepts as you can to refactor the code without breaking it
- A great approach is to write a series of helper methods that you can call from anywhere within your application


## Additional Resources
- [Object Oriented Analysis and Design with Applications, by Grady Booch and others](http://www.goodreads.com/book/show/424923.Object_Oriented_Analysis_and_Design_with_Applications)
- [Great lecture notes](https://atomicobject.com/resources/oo-programming/introduction-motivation-for-oo)
- [OOP in JS from JavascriptIsSexy](http://javascriptissexy.com/oop-in-javascript-what-you-need-to-know/)
- [Javascript, The Good Parts](http://www.goodreads.com/book/show/2998152-javascript)
- [Practical Object Oriented Design in Ruby, by Sandi Metz](http://www.poodr.com/)
