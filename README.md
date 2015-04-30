layout.js
=========

Stop using CSS for page layout!

Problem
-------

You want your HTML elements to line up visually: To do that, you must write multiple CSS classes, which may share magic numbers, and use them specially in HTML elements.  Additionally, your logical HTML must be rearranged to reflect the visual alignments you desire.  

Solution
--------

Construction lines simplify layout by acting as a common reference for visual elements.  This library implements a layout language that uses simple expressions to equate various construction lines to each other.  These equations are then "solved" to determine the position and sizes of all elements.
  
**Example: I want a footer at bottom of page**

```html
	<div id="footer" style="height:30px" layout="bottom=page.bottom; right=page.right; left=page.left">Footer</div>
```

Looking at the `layout` attribute:

* `bottom=page.bottom` - Bottom- of this element is congruent to the bottom of `page` 
* `right=page.right` - right of this element is congruent to the right of `page`
* `left=page.left` - left of this element is congruent to the left of `page`

We can solve these three equations to conclude the width is 100% of page width, and the `#footer` is always at the bottom of the `page`.

**Example: Center my chart in `#container`**

```html
	<div id="chart" class="chart" layout="middle=container.middle;center=container.center"></div>
```

Please notice that the vertical dimension uses `middle` while the horizontal dimension uses `center`.  

**Simpler formula**

You may use the first letter of each line in vertical/horizontal pairs to simply your formula.  For example, the above can be simplified to:

```html
	<div id="chart" class="chart" layout="mc=container.mc"></div>
```

Syntax
------

	<expression> := <line_reference> "=" <element_id> "." <line_reference>

The `<element_id>` is the id of another element, or some number of dots (`.`, `..`, `...`) used to refer to parent elements.  A `<line_reference>` is the name of a line (or point) of the element.  

Special Elements IDs
--------------------

There are two special element ids you may use: 

* `window` - refers to the browser window
* `page` - refers to the visible page (which is always at least as big as the browser window)


Line (and Point) References
---------------------------

Every element has common, named, construction lines: Vertical lines are `top`, `middle` and `bottom`.  Horizontal lines are `left`, `center` and `right`.  You may also refer to a vertical/horizontal pair (a point) by using the first letter of each respectively:

* `tl` - top-right corner
* `mc` - middle-center of element
* `br` - bottom-right corner

Instead of letters, you may also refer to ratios for more precise positioning:

**Example: sidebar is left third of window, scrolling will not affect it** 

	<div id="sidebar" layout="tl=window.tl;br=window.b[1/3]"></div>

There are really four formula here:

* `top=window.top` - assign top of `#sidebar` to top of `window`
* `left=window.left` - assign left of `#sidebar` to left of `window`
* `bottom=window.bottom` - assign bottom to bottom of window
* `right=window._[1/3]` - assign right of `#sidebar` to horizontal 1/3 of window

Vertically, these formula are solved to conclude the sidebar is 100% of the window height, it will not move while scrolling (because the construction lines of the window do not move).  Horizontally, the left is flush with the left side of window, and right side of `#sidebar` is at 1/3 of the way across the screen.


How does it work?
-----------------

The library translates the simple equations into more-complex combination of CSS, absolute/relative indicators and parent/child element relationships.  This means, for most layout, the markup is done once, and left to the browser to use native code for placement and rendering.

In the cases where elements refer to lines from more than one element; a Javascript function is run on `window.resize()` to correct the widths.   


Appendix A: CSS is bad for layout
---------------------------------

CSS with its flow-layout box model is not suited for laying out most web pages.  HTML tables are still better at managing vertical and horizontal alignment, despite the years of standards committee work.

**Example: I want a footer at bottom of page**

In theory, to do this, you should only need to declare just three facts:
* What element represents "the page"
* What element will contain your "footer"
* What does "bottom" mean

[The CSS solution is incredibly long!](http://matthewjamestaylor.com/blog/keeping-footers-at-the-bottom-of-the-page)  Kudos to the author for presenting this solution so the masses do not have to discover this on their own.  Here are the additional facts we must add to our program logic to align our footer: 

* Make an artificial `#container` to act as footer parent
* Markup `#container` as `position:relative` 
* Markup `#footer` as `position:absolute`
* Ensure `#footer` is child of `#container` 
* Markup `#container` to `height:100%`
* Markup `body` to `height:100%`
* Pass a magic number (height of footer) to the sister elements (`#body`, in this case) for margins

These extra declarations are required by CSS and not the problem domain!  These extra declarations are what make CSS needlessly complicated.  The footer is a good example of how hard it is to position any element on a page.

Appendix B: Non-Solution
------------------------

Meta-CSS languages add a layer on top of CSS to solve the magic number problem; allowing the programmer to assign them to variables.  The end result reduces redundancy, but is more abstract, and more complicated!  

