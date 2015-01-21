#Structuring Meteor Applications

This post is the second in a series of posts about the new JavaScript framework, Meteor. (The first post can be found [here](http://fourword.fourkitchens.com/article/getting-started-meteor).)

I'm going to discuss the importance of implementing proper structure patterns in Meteor applications, and give some advice that may help you organize your codebase. If you are unfamiliar with Meteor, check out the first ([introduction post](http://fourword.fourkitchens.com/article/getting-started-meteor)) in this series, or go through the demos available on [Meteor's project website](http://meteor.com). Let's get started!

##File organization
Organizing your source tree can be really difficult, but having a specifically defined pattern for this is crucial, especially as you scale your project. There are lots of source tree patterns available ([Maven being a popular one](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)). However Meteor is unique in that it doesn't follow the standard request/response format that most web applications do. 
As you might know, JavaScript in a Meteor project get’s published to the server, or the client, or to both. Meteor allows you to control where files get published by respecting a few folder names. Anything code outside of these folders is published to both the server and the client.

* `/client` - Files within this folder get published to the browser.
* `/server` - Files within this folder are run on the server only.
* `/public` - Files within in this folder can be served to the client. Used for media assets, like images and videos.
* `/private` - Files within this folder can be used by the server using the [Assets API](https://docs.meteor.com/#assets).

By putting your files in their appropriate folder you can ensure that your code is published to the proper environment. However if that is the extent of your organization pattern, you'll quickly realize that it isn't enough. As your codebase grows, blocks of code will get lost, and the project will become a pain to work on. You can evolve the project's structure patterns as you go, but that ends with pain as well, because as your organization techniques evolve, you'll have to update existing code to fit the new standard. This will result in a lot of wasted time. Because of this, it's vitally important that you think about your project's structure before starting to work on it.

###Organizing files by feature
I've found that one of the best ways to organize files is to group them by feature. (I refer to "sets of features" as "modules", just for clarity.) For instance, if you have a feature of your application that handles user dashboards and login details, they should be grouped into folders that have the same name within their environment-specific folder, like so:

* `/client/modules/user_dashboard` - Holds view files, interface event handlers, route definitions, etc.
* `/server/modules/user_dashboard` - Holds publication functions, permission settings, etc. 
* `/lib/modules/user_dashboard` - Holds collection definitions, and data validation/formatting functions, etc.

This organization pattern helps developers separate code files by module. Using this pattern enables multiple developers to work on the same project without conflict. It also makes maintenance a lot easier, and allows for organized documentation.

The difficult thing about this pattern is knowing when a block of code should be placed in an existing module, and when it should be in it's own module. I've found that following one rule can help mitigate this problem: If a piece of code is directly/exclusively used by, specifically related to, and accurately defined by a module that currently exists, place it in that module. If a piece of code may have multiple defendants and isn't specifically related to any of it's dependents, it should be it's own module.

###Building features in packages
Meteor has a powerfull [package system](http://docs.meteor.com/#/full/writingpackages) that allows portable feature sets to be created. Dependencies between packages and npm modules can be officially and explicitly declared. Individual packages can be versioned. Packages can also be re-used in other projects, which can be really useful! Most of the time I build my modules into packages, and I implement the same `/client`, `/server/`, and `/lib` folder structure patterns within each package. Consistent organization patterns make for easier maintenance! :)


##Code structure and organization
Code structure/organization is a difficult topic of discussion as it's highly subjective, but here are some do's/don'ts that may help you if you're just starting out:

###DO document your code
This seems obvious, but I've noticed a lazy documentation trend amongst the Meteor projects I've worked on. Consider implementing something like [usejsdoc.org](http://usejsdoc.org/). The person that ends up supporting the application you build will thank you. Be sure to define your code-level dependencies, and write `README` files that document your individual modules.

###DO organize your code consistently
If you create Meteor subscriptions at the top of your view files, then do it consistently. If you put your route definitions right after your subscriptions, then do it across all your view files. Etc, etc, etc.

###DO separate your templates into different files
You *can* put as many blaze templates into one file as you want, and sometimes it's ok to do so, especially if two templates are exclusively related. However, I've found that letting most templates have their own files is really helpful, especially when you or someone else is trying to find your template later on.

###DON'T overuse `Meteor.isClient` and `Meteor.isServer`
By "don't overuse" I mean "don't place 200 lines of code in one of these environmental conditionals".

###DON'T over compartmentalize code
If a module is one code file and 12 lines of code, consider the possibility of merging it into an appropriate module. If you end up with lots of random helper functions, then create a `/helpers` directory, and put all those little helpers in there.

###DON'T rely on grep to find things
If you're losing pieces of code and having to grep around to find them, something isn't quite right. Document your code and maintain consistent structure patterns so that you and others can navigate your codebase.

##An example
What better way to demonstrate than to give an example! I maintain a [boilerplate](https://github.com/patrickocoffeyo/meteor-boilerplate) that may be helpful when you're starting a project. It's still under development, but it might give you some helpful patterns to follow. If you have any suggestions or ideas, please feel free to fork the project and send me a pull request! :)



  

