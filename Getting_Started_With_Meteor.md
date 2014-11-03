#Getting Started With Meteor

There's been a lot of talk recently about a new JavaScript framework called [Meteor](http://meteor.com), especially since the project just recently reached version 1.0. So, what is Meteor? Why is it different than the hundreds of other JS frameworks available? I’ve had the opportunity to build several Meteor applications using the beta, all of which are now working in a production environment, and through these experiences I’ve learned a lot about about how Meteor works, so I thought I'd share some of my knowledge with a series of blog posts. This post is the first in a series of post that will explain what Meteor is, why it's important, how to get started with using it, how to properly structure your Meteor codebase, and how to build large, mantainable applications in it. 

This post is geared towards people who are completely new to Meteor, and is a basic list of instructions for installing Meteor, creating a Meteor app, and running your app for the first time. I'd encourage you to take a look at the [official documentation](docs.meteor.com) and play around with the APIs available before attempting to build anything serious. With that said, let's get started!

##What is Meteor?
In short, Meteor is a JavaScript framework that enables developers to quickly create reactive applications that are highly accessable. It comes with a well-baked package system, tools for publishing to mobile devices using Cordova, deployment tools. And yet, it is surprisingly agnostic as to how your application should be structured. It simply serves as a collection of tools that stand between your server side code and your client side code, and facilitates the organized transfer of data between the two.

##Installing Meteor
###Linux/OSX
It's very simple on a unix-based operating system. Run this code in your command prompt:

```curl https://install.meteor.com | /bin/sh```

###Windows
If you're developing on a Windows system, visit [this link](http://win.meteor.com) and follow the instructions.


Once you have finished installing Meteor, running `meteor --version` should return the latest version of Meteor. Success!


##Creating and running your first Meteor app
This is also very simple. Go to your console, and change your working directory to one that you would like to put your app in. Then run this:

```meteor create myAppName```

This command will create a directory in your present working directory called `myAppName`. Inside of the directory you should find a js, css, and html file. You should be able to run your Meteor app simply by running `meteor` in your app's directory. Once Meteor has finished it's startup processes, you'll be able to go to `http://localhost:3000` in a browser, and see the sample Meteor app as defined in the default js/css/html files in your app root.

##Basic principles
There's a couple things you should know about Meteor before starting to write your code. If you don't understand these basic principles, you may get confused as to what code is running where, and when.

###Client and server code
Meteor publishes code to either the client (often your browser), the server, or to both. For instance, code that deals with template interactions should run only on the client. Code that publishes data from MongoDB to the client should only run on the server. And code that defines references to MongoDB collections should be run on both the client and the server. So how should one tell Meteor where code should run?

There are two ways of ensuring that code runs in a specific environment. The first way involves putting js files in specific directories. Meteor respects a few folder names:

* `/client` - Files within this folder get published to the browser.
* `/server` - Files within this folder are run on the server only.
* `/public` - Files within in this folder can be served to the client. Used for media assets, like images and videos. Files are acessable to the client from the root.
* `/private` - Files within this folder can be used by the server using the [Assets API](https://docs.meteor.com/#assets).

The second way involves wrapping code blocks in conditionals that check the environment variable, like so:

```if (Meteor.isClient) {
  // Do some stuff on the client.
}```

```if (Meteor.isServer) {
  // Do some stuff on the server.
}```

Code that isn't in a client/server folder, and isn't wrapped in a conditional as demonsrated above gets published to both the client and the server.

###Collections
Meteor stores persistent data in collections in the form of JavaScript objects called documents. Once created, these collections act like a MongDB collection but will allow your code to interact with documents both on the client and the server. Creating a collection is very easy. In a file that is loaded on both the client and the server, you can create a collection like so:

```MyDocuments = new Mongo.Collection("mydocuments");```

Once your collection has been created, you can then insert, update, delete, and return documents. Here are some examples:

```
MyDocuments.insert({
  title: "A New Document",
  author: "Patrick Coffey",
  body: "This is my document body."
});

MyDocuments.find({
  author: "Patrick Coffey"
});

MyDocuments.update({
  title: "A New Document"
}, {
  $set: {
    body: "Penguins are cool. Tux is my favorite!"
  }
});

MyDocuments.delete({
  title: "A New Document"
});
```

As mentioned previously, these operations can be done on either the client, or the server, and changes to a document will be synced between the two.


###Publish/subscribe methods
Meteor provides a mechanism that sort of "sycs" data between the server and the client. The server publishes data sets to the client, and the client can (conditionally) subscribe to that publication. Here is an example using our `MyDocuments` collection:

```
// Publishes documents by author.
if (Meteor.isServer) {
  Meteor.publish("MyDocumentsByAuthor", function (authorName) {
    // Make sure argument is a string.
    check(authorName, String);
    
    // Return a cursor for the MyDocuments collection.
    return MyDocuments.find({
      author: authorName
    });
  });
}

// On the client, subscribe to "MyDocumentsByAuthor" for the author "Patrick Coffey".
if (Meteor.isClient) {
  Meteor.subscribe("MyDocumentsByAuthor", "Patrick Coffey").ready(function() {
    console.log('yay, subscription has been formed and data is available!');
  }));
}
```

I'd encourage you to read the [documentation](https://docs.meteor.com/#/basic/pubsub) on publishing/subscribing in Meteor.

##A helpful boilerplate
Through my experiances with Meteor, discovered some useful organization patterns and some packages that enable a lot of commonly nessecary functionality. So, I put together a quick boilerplate for Meteor. It's a good starting point for Meteor applications, and it may help clarify some parts of Meteor, as it is a working example. Check it out on [Github](https://github.com/patrickocoffeyo/meteor-boilerplate)!
