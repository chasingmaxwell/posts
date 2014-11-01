#Structuring Meteor Applications

There’s been a lot of talk recently about a new reactive JavaScript framework called Meteor. Recently Meteor reached v1.0, and it’s been getting a lot of attention from both critics and fans. I’ve had the opportunity to build several Meteor applications using the beta, all of which are now working in a production environment, and through these experiences I’ve learned a lot about about how a Meteor application should be organized to be scalable, maintainable, and easy to work even as the project grows. 

Meteor by design is very agnostic as to how code should be organized, and so when designing an application to run in the Meteor framework, one should give careful thought to the file/code structure to avoid having to refactor code in the future. In this post, I’m going to give some basic guidelines and tips for those looking to start a Meteor project. 

##Project file structure
In Meteor, JavaScript get’s published to the server, the client, or to both. Meteor allows you to control where files get published by respecting a few folder names. Anything code outside of these folders is published to both the server and the client.

* `/client` - Files within this folder get published to the browser.
* `/server` - Files within this folder are run on the server only.
* `/public` - Files within in this folder can be served to the client. Used for media assets, like images and videos. Files are acessable to the client from the root.
* `/private` - Files within this folder can be used by the server using the [Assets API](https://docs.meteor.com/#assets).

By putting your files in their appropriate folder you can ensure that your code is available to the proper environemnt. However if that is the extent of your organization pattern, you'll quickly realize that it isn't enough. As your codebase grows, blocks of code will get lost, and the project will become a pain to work on. You can organize as you go along, but that results in the same problem. As your organization techniques evolve, you'll have to update existing code to fit the new standard and lots of time will be wasted. Because of this, it's vitally important that you think about your project's structure before starting to work on it.

###Organizing files and code by feature








  