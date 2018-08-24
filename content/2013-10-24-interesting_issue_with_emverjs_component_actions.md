Title: An Interesting Issue with Ember.js Component Actions
Date: 2013-10-24
Category: Programming
Tags: Ember.js, JavaScript

I have been working on a project for work and ran into an issue with passing actions out of an Ember controller.
I am using Ember 1.0.0. I found that the documentation as provided is unclear.

So basically I have a component that has an action and I want to pass that action out to the parent controller.
The [documentation][1] makes it appear that the following code is all that is needed:

    :::javascript
    App.MySpecialComponent = Ember.Component.extend({
        actions: {
            click: function() {
                this.sendAction('mySpecialClicked');
            }
        }
    });

    App.IndexController = Ember.Controller.extend({
        actions: {
            mySpecialClicked: function() {
                // perform some action here.
            }
        }
    });

Unfortunately, this does not work.
Instead the App.MySpecialComponent class must have a property named the same as the string passed to `this.sendAction()`.
The [API documentation][2] says this in the text but the code samples do not show it.
So instead your App.MySpecialComponent should look like this:

    :::javascript
    App.MySpecialComponent = Ember.Component.extend({
        clicked: 'mySpecialClicked',

        actions: {
            click: function() {
                this.sendAction('clicked');
            }
        }
    });

I really like the way that ember.js works and this is probably only a minor annoyance.
However, I thought I would share in case someone else found themselves in the same quandary.

[1]: http://emberjs.com/guides/components/sending-actions-from-components-to-your-application/
[2]: http://emberjs.com/api/classes/Ember.Component.html#method_sendAction
