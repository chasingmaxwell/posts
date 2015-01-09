#Structuring Meteor Applications

This post is the second in a series of posts about the new JavaScript framework, Meteor. (First post can be found [here](http://fourword.fourkitchens.com/article/getting-started-meteor).)

In this post, I'm going to discuss the importance of implementing proper patterns of structure and architecture from the begginning when building a Meteor application. The post will be broken up into three sections; file structure/organization, code structure/organization, enforcing coding standards. If you are unfamiliar with Meteor, I'd suggest you checkout the [introduction post](http://fourword.fourkitchens.com/article/getting-started-meteor), or go through the demos available on [Meteor's project website](http://meteor.com). Let's get started!

##File structure/organization
As you might know, JavaScript in a Meteor project get’s published to the server, the client, or to both. Meteor allows you to control where files get published by respecting a few folder names. Anything code outside of these folders is published to both the server and the client.

* `/client` - Files within this folder get published to the browser.
* `/server` - Files within this folder are run on the server only.
* `/public` - Files within in this folder can be served to the client. Used for media assets, like images and videos. Files are acessable to the client from the root.
* `/private` - Files within this folder can be used by the server using the [Assets API](https://docs.meteor.com/#assets).

By putting your files in their appropriate folder you can ensure that your code is published to the proper environemnt. However if that is the extent of your organization pattern, you'll quickly realize that it isn't enough. As your codebase grows, blocks of code will get lost, and the project will become a pain to work on. You can evolve your structure patterns as you go, but that ends with pain as well. As your organization techniques evolve, you'll have to update existing code to fit the new standard and this will result in a lot of wasted time. Because of this, it's vitally important that you think about your project's structure before starting to work on it.

###Organizing files and code by feature








  