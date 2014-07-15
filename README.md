wcDocker (Web Cabin Docker) is a page layout framework that gives you dynamic docking panels.  Panels can float on their own or be docked on any side of any other panel.  All panels can be moved, resized, removed, and created at will by the user (unless otherwise restricted).  This project is currently under development by Jeff Houde (lochemage@gmail.com).  wcDocker requires the JQuery library, currently developed using version 2.1.1 although earlier versions should work as well.

For a currently working demo, try it here [http://docker.webcabin.org](http://docker.webcabin.org)

### The following features are supported currently: ###

* Panels can be docked to any side of any other window.
* Panels can be detached from a dock position to float on their own, and then dock again.
* Panels can be resized.
* Panels can be closed via close button.
* Panels can be initialized in code for a default layout.
* Panels can be programmed with a minimum and maximum size constraint.
* Multiple panels of the same type can be created.
* Panels can be created via context menu.
* Event system to react on panel changes.
* Ability to group multiple windows into the same dock panel via tabbing.

### The following features are still under development: ###

* Save/Restore support for remembering a user's window setup.
* A possible option of restricting the number of total panels created.
* A possible option of restricting the total copies of the same panel type.
* Allow styling of window colors and other options either by changing css files or an internal color picker system.

****

# Basic Implementation #

For more detailed information on the API, check out the [wiki](https://bitbucket.org/WebCabin/wcdocker/wiki/Home).

Begin by creating an instance of the main docker window and assign it a DOM container element.
Typically this would be the document body, but there is no restriction if you want to use a
smaller area instead.  Multiple main windows can be used, however, no support exists for
cross interaction between them, nor do I plan on implementing that.  Also note that floating
windows are not constrained to the given container element, they can float anywhere in the browser window.
```
#!javascript
var myDocker = new wcDocker(document.body);
```
The docker window contains a central panel which can not be moved or hidden and should be
filled with content for your main view.  Use the center() function to access its layout.

```
#!javascript
var myLayout = myDocker.center();
```
What you get from the central panel is the layout, layouts are the container where all your window's content will be placed.
Each layout is a table grid that is dynamically resized based on content given. To add a DOM element, add it into your
layout with an (x, y) grid location. You can also optionally include width and height values which will stretch an element
over multiple grid cells within the layout.
```
#!javascript
myLayout.addItem(myElement, x, y, width, height);
```
The main docker window also contains docking panels, which can be moved around and organized at will by the user.
All docking panels have to be registered first before use, this allows the docker to know what types
of docking panels are available and allow the user to add and remove them at will.  To register a new type,
you will need a unique name to identify it and a function callback which allows you to initialize the panel type when
one is created.  Note, there is also an optional third parameter to mark it private, by supplying a true value the user will not be able to create an instance of this panel type.
```
#!javascript
myDocker.registerPanelType('Some type name', function(myPanel) {});
```
Inside the callback function you are given the panel that was just created, from here you can
access its layout and populate it, much the same as done with the central panel.
```
#!javascript
  myPanel.layout().addItem(myElement, x, y, width, height);
```
From here you can set various starting properties of the panel, such as
the desired or the minimum size:
```
#!javascript
  myPanel.size(200, 200);
  myPanel.minSize(100, 100);
});
```
You can also register some events if you feel you need them:

```
#!javascript

  myPanel.on(wcDocker.EVENT_RESIZE, function(myPanel){});
```
The following event types are supported by the Docker:

wcDocker.EVENT_CLOSED   = 'closed' = When the panel has been closed and is about to be destroyed.

wcDocker.EVENT_ATTACHED = 'attached' = When the panel has changed from a floating panel to a docked panel.

wcDocker.EVENT_DETACHED = 'detached' = When the panel has changed from a docked panel to a floating panel.

wcDocker.EVENT_MOVED    = 'moved' = Whenever the position of the panel has changed.

wcDocker.EVENT_RESIZED  = 'resized' = Whenever the size of the panel has changed.

You can also create your own custom events by supplying your own event names, and then later triggering
them on your own either from the main Docker instance or from your own panel.  The second parameter you give
is can be any data object that you wish to pass into all receiving handler functions:
```
#!javascript
myDocker.trigger('your event', customDataObj);

// or

myPanel.trigger('your event', customDataObj);
```
Registering a panel type does not actually create an instance of it into your window.  To create an instance,
use the addPanel() function in the docker.
```
#!javascript
myDocker.addPanel('Registered type name', wcDocker.LEFT, false, optionalTargetPanel);
```
The first parameter is the name of the panel type you have previously registered.
The second parameter is an enumerated value that determines the location where this window will be docked;
It can be one of the following:

wcDocker.DOCK_FLOAT    = Make a floating window that is not docked.

wcDocker.DOCK_LEFT     = Dock it to the left side of the central or target panel.

wcDocker.DOCK_RIGHT    = Dock it to the right side of the central or target panel.

wcDocker.DOCK_TOP      = Dock it to the top of the central or target panel.

wcDocker.DOCK_BOTTOM   = Dock it on the bottom of the central or target panel.

The third parameter determines whether this window is allowed to group up (via tabs) with another, already existing,
panel or if a new panel should appear next to it.

The final parameter is optional, normally docked windows will dock in relation of the main docker's central
panel. However, by supplying a specific panel instead, your new panel will be docked in relation to the target.
The return value is the newly created docking panel, in the case that you may want it.

For more detailed information on the API, check out the [wiki](https://bitbucket.org/WebCabin/wcdocker/wiki/Home).

****

# End User Usage #

For the most part, arranging your windows is as simple as a drag-drop operation.  Click the title-bar of any panel frame and drag to begin moving an entire panel frame, or just click and drag one of the tabs to move a single panel.  You can drag the panel(s) into the title bar of another frame to merge yours into it, you can drag them to the edge of another frame and split that frame into two, or you can drag it anywhere that does not anchor to make it into its own floating window.

To create new panels, right click on the position where you want the new panel to be inserted and choose the the panel to create within the menu.