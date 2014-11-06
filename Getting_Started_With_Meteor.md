#Getting Started With Meteor

There's been a lot of talk recently about a new JavaScript framework called [Meteor](http://meteor.com), especially since the project just reached version 1.0. I’ve had the opportunity to build several Meteor applications using the beta, all of which are now running in a production environment. Through these experiences I’ve learned a lot about about how Meteor works, so I thought I'd share some of my knowledge. This post is the first in a series of posts that will discuss what Meteor is, getting started with Meteor, writing secure Meteor applications, and properly structuring your codebase. 

This post is geared towards people who are completely new to Meteor and is a basic list of instructions for installing Meteor, creating a Meteor app, and running your app for the first time. I'd encourage you to take a look at the [official documentation](docs.meteor.com) and play around with the APIs available before attempting to build anything serious. With that said, let's get started!

##What is Meteor?
In short, Meteor is a JavaScript framework that enables developers to quickly create reactive applications that are highly accessible. It comes with a well-baked package system, tools for publishing to mobile devices using Cordova, and deployment/testing tools. And yet, Meteor is surprisingly agnostic as to how your application should be structured. It simply serves as a collection of tools that stand between your server side code and your client side code, and facilitates the organized transfer of data between the two.

##Installing Meteor
###Linux/OSX
Installing Meteor on a unix-based operating system is very simple. Run this code in your command prompt:

```curl https://install.meteor.com | /bin/sh```

###Windows
If you're developing on a Windows system, visit [this link](http://win.meteor.com) and follow the instructions.


Once you have finished installing Meteor, running `meteor --version` should return the latest version of Meteor. Success!


##Creating and running your first Meteor app
This is also very simple. Go to your console, and change your working directory to one that you would like to put your app in. Then run this:

```meteor create myAppName```

This command will create a directory in your present working directory called `myAppName`. Inside of the directory you should find a js, css, and html file. You should be able to run your Meteor app simply by running `meteor` in your app's directory. Once Meteor has finished it's startup processes, you'll be able to go to `http://localhost:3000` in a browser, and see the sample Meteor app as defined in the default js/css/html files in your app root.


##Package Manager
By design Meteor is very light in it's vanilla state and doesn't contain a bunch of features. However, it comes with a package management system and lots of great packages are available on [Atmosphere](https://atmospherejs.com). These packages provide useful features like routers, user role systems, and file managers. It's easy to install packages. In your command line, run:

```
meteor add myPackageName
```

... and to remove a package:

```
meteor remove myPackageName
```

Often times the package name will consist of a user name and a repository name. For example,  `patrickocoffeyo:aCoolPackage` would refer to a package named `aCoolPackage` that I published using my Atmosphere user, [patrickocoffeyo](https://atmospherejs.com/patrickocoffeyo).

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

Code that isn't in a client/server folder, and isn't wrapped in a conditional as demonsrated above, gets published to both the client and the server.

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

It should be noted that there is a package called `autopublish` that's installed by default. It's really useful when prototyping as it simply publishes all data to the client. Past testing and prototyping, this package is useless and actually harmful. As soon as you make a publish/subscription you'll need to remove the package because you'll no longer need it. This can be done by running `meteor remove autopublish`.

I'd encourage you to read the [documentation](https://docs.meteor.com/#/basic/pubsub) on publishing/subscribing in Meteor.

###Templating system
Once you have defined a collection, added some documents to your collection, and published those documents to the client, you can present your data to a user by rendering it using Meteors reactive template system, [Blaze](https://docs.meteor.com/#/full/blaze). You can easily pass document objects to the template layer:

```
if (Meteor.isClient) {
  Template.myTemplateName.helpers({
    myDocuments: function() { 
      return MyDocuments.find({
      	author: "Patrick Coffey"
      });
    }
  });
}
```

And in your template file, you can render data:

```
{{#each myDocuments}}
  Title: {{> title}},
  Author: {{> author}},
  Body: {{> body}}
{{/each}}
```

With the example document I created eariler, this would render in the browser as:

```
Title: A New Document,
Author: Patrick Coffey,
Body: Penguins are cool. Tux is my favorite!
```

Blaze is very versitile, I'll go into more detail on Blaze in a later blog post, but for now be sure to visit the [documentation](https://docs.meteor.com/#/full/blaze) for blaze.


##A helpful boilerplate
Through my experiances with Meteor, I've discovered some useful organization patterns and some packages that enable a lot of common and necessary functionality. So, I put together a quick boilerplate for Meteor. It's a good starting point for Meteor applications, and it may help clarify some parts of Meteor, as it is a working example. Check it out on [Github](https://github.com/patrickocoffeyo/meteor-boilerplate) and of course feel free to fork it and contribute.

##What's next?
In my next post about Meteor, I'm going to talk about building secure Meteor applications and address several commonly voiced security concerns. I'll also discuss the subscription/publication model in greater depth and show you how the to make secure publications.


