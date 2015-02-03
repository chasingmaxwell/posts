# Enforcing coding standards in Meteor

This post is the third in a series of posts about the new JavaScript framework, Meteor. If you are unfamiliar with Meteor, check out the first ([introduction post](http://fourword.fourkitchens.com/article/getting-started-meteor)) in this series, or go through the demos available on [Meteor's project website](http://meteor.com).

In this post, I'm going to discuss the importance of enforcing a strict and clear coding standards, and then give you some tools to help you define and enforce a coding standard within your Meteor applications.

## Why are coding standards important?
Coding standards are critical for succesfully developing, mantaining, and scaling an application's codebase. They ensure that the code is formatted consistently, and documented properly. In some cases they can even help developers catch bugs before they're added to the codebase. As you might have experienced, working unfamiliar code is a lot more feasible when the code is consistently formatted and documented with details that explain unclear or complicated code blocks. Coding standards can also help developers work together on a project. Instead of interatively disagreeing or argueing on a specific standard or pattern, developers can come to an agreement on the best set of standards to use throughout the life of the application.


## Coding standards in Meteor applications
I've read through a large number of the open source Meteor-based applications that people have published. There's some really cool stuff! But one thing that I've noticed is that most Meteor-based applications don't seem to have a defined coding standard. Often times the formatting and documentation patterns are inconsistent, especially if multiple people have worked on the project.