# An Introduction to OpenSCAD

## What is OpenSCAD?

Pronounced &ldquo;open-ess-cad&rdquo;, according to the OpenSCAD web site.

OpenSCAD is a *constructive geometry* 3D modeling program. It is less popular than some other 3D modeling tools such as FreeCAD, SketchUp, or Blender, but has some advantages for creating 3D part designs:

* Parts are created by writing a script, not with the mouse. This makes some things easier for part design, but other things harder.
* Can create libraries of part designs for others to use. Or use existing libraries such as [MCAD](https://github.com/openscad/MCAD).
* Parts can be *parameterized* to make then easy to customize.
* It&rsquo;s harder, in general, to create a non-solid model than with some other 3D modeling tools &ndash; important when 3D printing.
* Good for designing CAD models, bad for artistic modeling.


## Installing OpenSCAD

The latest release of OpenSCAD, as of this writing, is 2015.03-1. Download OpenSCAD from the OpenSCAD web site, http://www.openscad.org/downloads.html. There are installers for Windows, a DMG for OS X, and instructions for various Linux distributions.

## First Steps

When you start OpenSCAD a window will appear with three panes, an editor for modifying your OpenSCAD script, a view to display your 3D model, and a console for information and error messages.

<img alt="OpenSCAD window" src="images/openscad-window.png" style="max-width: 100%;" />

## Your First Model

In the editor window, enter this (very small) script and press F5 to render the object in the view pane. You can instead press the preview button
<img alt="OpenSCAD preview button" src="images/preview-button.png" width="37" height="42" />, which is the same as pressing F5, or use the menu option Design > Preview. After you have saved the file once, updating the preview is automatic as soon as the file is saved.

    cube();

You should then see a small cube shown in the view:

<img alt="Cube image" src="images/cube.png" width="373" height="241" />

### Manipulating the View

The cube is 1&times;1&times;1, which is quite small. Zoom the view using the zoom button
<img alt="Zoom button" src="images/zoom.png" width="37" height="41" />, or press the *zoom all* button
<img alt="Zoom-all button" src="images/zoom-all.png" width="36" height="38" /> to scale the view to match the size of your model. You can also use the mouse wheel or a zoom gesture on your touchpad. All of the commands that affect the view are in the View menu as well.

Clicking and dragging in the view rotates the axes around so you can see all sides of your model. By default the origin is in the center of the view, and the view rotates around the origin. You can drag the view laterally without rotation by Ctrl-click and drag. After that the view rotates around the new center of the view, rather than the origin. There are also buttons below the view panel for choosing particular eye positions, such as top, right, and so on.

### The Structure of OpenSCAD Scripts

The use of `cube();` above demonstrates 2 things about the structure of OpenSCAD scripts.

1. `cube()` is an example of a predefined OpenSCAD module. In this case it is what is called an *object module*, because it creates 3D objects. The parentheses are required when invoking a module.

2. The whole line &ndash; with the semicolon &ndash; is an example of an OpenSCAD *action*. (We would probably call it a *statement* in another language.) OpenSCAD scripts consist of a number of *actions*. Each action may span multiple lines but must end in a semicolon (or sometimes be wrapped in braces: `{` and `}`).

### Sizing the Cube

We obviously don&rsquo;t want cubes of only one size. We can also create *cuboids* where the sides have different lengths.

    cube(size=[10, 5, 8]);

This produces a *cuboid* of size 10 units on the X axis, 5 units on the Y axis, and 8 units on the Z axis.

<img alt="Cube with sizing" src="images/sized-cube.png" width="242" height="188" />

### Coordinate System

OpenSCAD is usually used to create STL files for 3D printing. STL files do not have a way to indicate a physical measurement to correspond to a unit of 1 in the STL file. Instead, the 3D printing or slicing program will have an option to specify whether to interpret the STL units as millimeters, inches, or some other measure.

I usually design parts using millimeters, a practice that appears to be common among others using OpenSCAD, but you can make your own decision how to handle units of measure.

The coordinate axes follow the right-hand rule where the arrangement of your thumb and first two fingers match the X, Y, and Z axes, respectively.

<img alt="Right-hand coordinate system" src="images/righthand.jpg" width="185" height="135" />

### Parameters to Modules

The addition of `size=[10, 5, 8]` is an example of passing a parameter to a module in order to modify what object it creates. The `cube()` module takes these parameters:

* `size` &ndash; Either a single number or a *vector* of three numbers giving the X, Y, and Z dimensions. If you supply a single number, it will be used for all three dimensions.
* `center` &ndash; If `false`, a corner of the cuboid will be at the origin. If `true`, the center point of the cuboid will be at the origin.

Creating a cuboid with and without centering:

<img alt="Centered cuboid" src="images/centered-cube.png" width="242" height="189" />
&nbsp;&nbsp;&nbsp;
<img alt="Cuboid not centered" src="images/sized-cube.png" width="242" height="188" />

Parameters in OpenSCAD work a little differently than parameters in some other languages.

* They may be omitted if there is a default value. For `cube()`, for example, `center` defaults to `false` and `size` defaults to `1`.
* They have names which may be provided. If they are not provided, then the parameters need to be in the right order. `cube()` expects `size` first, and `center` second, so `cube(size=[10, 5, 8])` and `cube([10, 5, 8])` are equivalent.
* If you use the parameter names, you can specify the parameters in any order.
* Some parameters may take values of different types. `cube()` allows `size` to be a number or a vector, for example.
* You can specify parameters the module does not expect. These will be silently ignored. (A bad design decision, IMHO, but probably caused by the handling of *special variables* &ndash; see below.)

Based on these characteristics of parameters, all of these actions are equivalent ways to create a 10&times;5&times;8 cuboid:

    cube(size=[10, 5, 8]);
    cube([10, 5, 8]);
    cube([10, 5, 8], false);
	cube(center=false, size=[10, 5, 8]);
	cube(size=[10, 5, 8], false);
	cube([10, 5, 8], center=false);

Note that the only way to specify `center` first is to include the names of both parameters.

### Data Types in OpenSCAD

In the examples above, we see three different types of values that can be specified as parameters.

* *numbers* &ndash; In OpenSCAD these are always floating-point numbers. IEEE784 representation is used, giving about 17 digits of precision.
* *vectors* &ndash; Vectors are sequences of values enclosed in the square brackets `[` and `]`, and separated by commas. Vectors can hold items of any type, including other vectors: `[[1, 2, 3], [4, 5, 6]]`
* *boolean values* &ndash `true` and `false` are built-in boolean constants. There are also operators which give boolean results.

In addition, OpenSCAD supports *string values* enclosed in double quotes.

    "hello"

Some of the standard escape sequences are valid, including `\"`, and also the ability to specify Unicode code points using hexadecimal, such as `\u201D`.

    "this is a string with a quote \" in the middle"

### More View Manipulations

Now that we have a cuboid that has differing edge lengths, we can tell the difference between a view from a different side. There are six standard viewpoints to look down each axis in either direction, right, top, bottom, left, front, and back. These are all available in the View menu, or you can use the buttons below the view pane:

<img alt="Six standard viewpoints" src="images/standard-views.png" width="266" height="38" />

Clicking and dragging the mouse rotates the view about the point in the center of the view, by default the origin. You can instead drag the view left or right by Ctrl-click and drag, or by dragging with the right mouse button. If you want to center the axes in the view again, press the &ldquo;reset view&rdquo; button
<img alt="Reset view button" src="images/reset-view-button.png" width="16" height="17" />.

By default the view shows a *perspective* projection. That is, a 2-dimensional view of the 3D scene in which objects in the view seem smaller as they recede away. To see this, zoom the view so that the cube takes up much of the area of the pane. Notice that the edges of the cube converge toward a vanishing point.

A perspective view is not always the best, because it makes it more difficult to determine whether object edges coincide. An alternative view is an *orthogonal* view, which can be selected via the menu by View > Orthogonal or by pressing the <img alt="Orthogonal view" src="images/orthogonal.png" width="42" height="37" /> button.

## Exercise 1

1. Create a cuboid that is 20 units long in the X direction, 3 in the Y direction, and 6 in the Z direction.

2. Modify your use of the `cube()` module to center the cuboid around the origin.

3. Rotate the view around using the mouse so that the narrow end of the cuboid is pointing toward you, but you can still see the top face of the object.

4. Zoom the view so you can see that the edges of the cuboid parallel to the X axis converge to a vanishing point.

5. Change the view to an orthogonal projection so that the edges now are parallel and don't converge to a vanishing point.

6. Use the buttons to select each of the 6 standard views.

7. Use Ctrl-click and drag to move the view around laterally, without rotation.

8. Reset the view back to a diagonal viewpoint.

[Exercise 1 solution](ex1-solution.md)

## Manipulating Objects

To place objects somewhere other than the origin, you use the `translate()` module. For example, this offsets a cuboid so that it sits on the X-Y plane, but 10 units along the X axis and 5 along the Y axis.

    translate(v=[10, 5, 0]) {
      cube(size=[20, 3, 6]);
    }

<img alt="Translated cube" src="images/translated-cube.png" width="219" height="92" />

The `translate()` module is our first example of what OpenSCAD calls an *operator module*, one which does not produce 3D objects, but modifies how other objects are rendered. `translate()` takes a single argument `v` which is a vector of the distances along the X, Y, and Z axes to offset the objects it is modifying. The objects to be offset are placed inside braces, `{` and `}`. If there is only one module to be operated on, the braces can be omitted. This script is equivalent to the one above:

    translate([10, 5, 0])
    cube(size=[20, 3, 6]);

In this case the parameter name `v` has also been omitted.

### OpenSCAD Coding Style

OpenSCAD is a language similar in syntax to C++ and Java. For that reason, using a writing style similar to what you might use in C++ or Java makes sense. The rest of the examples herein will use these rules. But they are my rules, not necessarily what you will see in other OpenSCAD scripts.

* Opening braces will be on the same line as the operator they follow.
* If braces are used to surround the operands of an operator module, the contents inside the braces will be indented. The indent amount will be two spaces. (I usually use 4 spaces for Java and C++, but the level of nesting is usually smaller in OpenSCAD, making the smaller amount of indentation workable.)
* If braces are not used to surround the operand of an operator module, the operand will use the same level of indentation as the operator.
* The parameter name will be omitted if a module takes a single parameter and included otherwise.

### Drawing Multiple Objects

To draw more than one object, just put the actions one after anohter. For example, this draws a wall with an entranceway.

    cube(size=[8, 2, 6]);
    
    translate([12, 0, 0])
    cube(size=[8, 2, 6]);
    
    translate([18, 0, 0])
    cube(size=[2, 20, 6]);
    
    translate([0, 18, 0])
    cube(size=[20, 2, 6]);
    
    cube(size=[2, 20, 6]);

<img alt="Walled enclosure" src="images/walled-enclosure.png" width="169" height="107" />

## Getting Help

The OpenSCAD documentation is viewable in a web browser. There are links in the Help menu to launch a browser window to the help pages. The most important of these links are:

* Help &gt; Cheat Sheet &ndash; This launches a quick-reference page of all the modules and functions &ndash; we haven&rsquo;t talked about those yet &ndash; available. The documentation on each is usually quite good. All the parameters are explained, usually with examples.

* Help &gt; Documentation &ndash; This launches a menu of available documentation, including the OpenSCAD language reference. The language reference has information on some topics, such as data types, which are not listed on the cheat sheet.

## Exercise 2

1. Draw an arch using three cuboids, like the image below. Make the two uprights and the crossbar have the same cross-section, and the overhang of the crossbar the same on each end. (The exact dimensions aren&rsquo;t important.)<br />
<img alt="Pi-shaped arch" src="images/arch.png" width="179" height="130" />

2. Manipulate the view to see each side, to make sure the arch is symmetric.

3. Modify the crossbar so there is no overhang past the upright posts.

4. Move the uprights closer together. Make sure the crossbar stays flush with the uprights at each end.

5. Use the OpenSCAD cheat sheet to look at the documentation on the `scale()` operator module. Use `scale()` to double the size of your arch. Hint: Use braces, `{` and `}`, to group together what you want to scale.

[Exercise 2 solution](ex2-solution.md)