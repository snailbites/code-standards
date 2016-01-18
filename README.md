# UMAMI Stylesheet Guide

This document helps explain some of the conventions and practices used in building the stylesheet(s) for the UMAMI project.

# Code Standards

We've borrowed plenty of ideas in the formation of this document. Ultimately, what you are reading has been strongly influenced by the ideas of Medium's style guide and Nicole Sullivan's Style Guide Driven Development. What you are seeing is a mix of OOCSS and BEM. For more information, read here:

* [Medium's style guide](https://medium.com/@fat/mediums-css-is-actually-pretty-fucking-good-b8e2a6c78b06)
* [OOCSS](http://www.smashingmagazine.com/2011/12/12/an-introduction-to-object-oriented-css-oocss/)
* [BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)
* [Style Guide Driven Development](https://www.youtube.com/watch?v=ldW7zVmqu5g)

### Comments
We use C style commenting in our LESS. Add file-level comments at the top of every file, describing the file in the following format:

```css
/**
* @name         Simple name for the file (i.e., Search_Widget)
* @desc         Description of the file.
* @tested       browser 1, browser 2
* @url          an example URL where this code can be seen
*/
```

For important sections within the same document, break them out according to priority. Use *'s for highest priority, and -'s for secondary priority.

```css
/*****************************************************
* Globals
******************************************************/
@brandRestoHeaderRatingSize: 24px;
@brandRestoHeaderBlur: 20px;

/*------------------------------------------------------
 * Toggle Button
 *-----------------------------------------------------*/

.section-toggle {
    ...
}
 
```

Comments should go on the same line as the CSS style. Magic numbers, odd values, and things that might confuse other developers should always be commented.

```css
/* Good - gives helpful explanation to other developers */
.container {
  padding: 37px; /* This is needed to match the design of the gutter */
}

/* Bad - no comment */
.container {
  float: left !important;
}
```

### Class Names

All selectors for a particular component start with the wrapper class name. Class names should be camel case with the first letter lowercase.

```css
/* Good - Use camel case */
.thisIsGood {}

/* Bad - don't use underscores */
.this_is_bad {}

/* Bad - don't use dashes */
.this-is-bad {}
```

Children of the wrapper class should take on a dash and be written in camelcase. Sections with more than two dashes should consider creating a new parent style and start your naming again recursively.

```css
/* Good - wrapper and one child */
.sectionParent-sectionChild {}

/* Bad - too many children */``
.sectionParent-sectionChild-sectionChild2-sectionThisIsSparta {}
```

Class names can take on modifiers. If a class needs a simple property change, add the modifier to the end of the class, perceded by two dashes. Examples of modifiers are 

```less
/* Good */
.site {
    ...
}
.site--blue {
    color: @colorBlue;
}

/* Bad - dont chain classes, create a new one */
.site.blue.red {
...
}

/* Bad - this is a subsection, not a modifier */
.site {
    ...
}
.site--childElement {
    color: @colorBlue;
}

/* Good - nested child with modifier still its own class */
<div class=“someContainer”>
  <div class=“newThing”>
  </div>
</div>

.someContainer {
    ...
}
.someContainer-newThing {
    ...
}
.someContainer-newThing—-blue {
    ...
}

```

Change of state, which is driven by angular/javascript (ie. classes tacked on using ng-class or a directive), should be named in a boolean fashion.

```
/* Good */
<button ng-class=“{ ‘isBlue’ : isClicked() }”>

.isBlue {
    color: @colorBlue;
}

/* Good - one level nested within parent scope */
<div class=“someContainer” ng-class=“{‘isActive’ : ‘1 === 1’}”>
</div>

.someContainer {
    ...
    
    &.isActive {
        ...
    }
}

/* Bad - non descriptive and overqualified selector */
<button ng-class=“{ ‘blueBtn’ : isClicked() }”>

button.blueBtn {
...
}

```

s- should only be used when extending an existing bootstrap class. If we're not extending a bootstrap class. There is no other need to create a new class with the prefix s-.

```scss
/* Bad - There is no need to use s- as a prefix */
.s-layout-module {
    ...
}

/* Good */
.layoutModule {
    float: left;
}

/* Good - This is only acceptable when you cannot directly control the markup to add a new class */
.someModule > .s-btn {
    color: red;
}
```

### Nesting

Nesting should be limited to one level deep. More than two selectors become non-performant. 

```scss
/* Good */
.site {
    .inner {
      ...
    }
}

/* Bad - more than 1 level of nesting */
.site {
    .inner {
      ...

        .title {
         ....

            .subtxt {
                ...

                .element {
                    ...

                }
            }
        }
    }
}
```

If nesting is unavoidable, it is recommended to use the child selector, to limit the strain on performance.

```
<div class=“someContainer”>
  <div class=“newThing”>
    <h1>title</h1>
    <iframe>
    </iframe>
  </div>
</div>

/* Good - 
.newThing {
  & > h1 {}
  & > iframe {} 
}
```

It is also acceptable to use LESS nesting to create modifier classes. In certain cases it is more helpful to other developers to see groups of classes clustered together in a parent class. However, this can easily be confused with unacceptable nesting, so should be used carefully. Keep in mind that overuse of this technique can counteract the avoidance of nesting as stated earlier.


```less
/* Good - 
.someModule {
    ...
    
    &-inner {
        ...
    }
    
  &--blue {
      color: blue;
    }
    
}
```


### Pre-Processor Variables

Use pre-processor variables whenever possible. Variables allow us to quickly reuse values and have a change once mentality. Variables should be created/used for things like fonts, line height, padding, margin etc. Please make sure that if you need a value that either greater than +10 or * 4 more, a new variable should be created (if this value is needed in more than one place/the px value should be used as it'll be easier to read)

```scss
/* Good */

@fontSizeLarge = 14px;

.text--large {
    font-size: @fontSizeLarge;
}

.text--larger {
    font-size: @fontSizeLarge + 4;
}

/* Bad */
@fontSizeLarge = 14px;

.text--large {
    font-size: 14px;
}

.text--larger {
    font-size: @fontSizeLarge + 20;
}
```

It is best practice to compose units like font-size and margins by multiples and additions/substractions of these variables. Multiplying by more than 4 is counter-productive and should be avoided. It is acceptable to use a magic number in this case.

```scss
/* Good */

.someModule-someElement {
    margin-left: @padding-base-vertical * 3;
}

/* Bad */

.someModule-someElement {
    margin-left: 15px;
}

/* Bad */
.someModule-someElement {
    margin-left:  @padding-base-vertical * 17;
}

```
 

Declare extendable classes first in a declaration block with one line of whitespace beneath them.

```scss
/* Good */
.site {
    .transition(background, .3s, ease);
    .text-left;
    
    color: #555;
    font-size: 11px;
}

/* Bad */
.site {
    color: #555;
    .transition(background, .3s, ease);
    font-size: 11px;
    .text-left;
}

### Indentation

Each indentation level is made up of four spaces. Do not use tabs. (Please set your editor to use four spaces)

```css
/* Good */
.site {
    color: #fff;
    background-color: #000;
}

/* Bad - all on one line */
.site {color: #fff; background-color: #000;}
```

Rules inside of `@media` must be indented an additional level.

```css
/* Good */
@media screen and (max-width:480px) {
   .site {
       color: green;
   }
}
```

### Brace Alignment

The opening brace should be on the same line as the last selector in the rule and should be preceded by a space. The closing brace should be on its own line after the last property and be indented to the same level as the line on which the opening brace is. Keep in mind that you are not saving bytes by this. Spaces are ultimately removed in the build process.

```css
/* Good */
.site {
    color: #fff;
}

/* Bad - closing brace is in the wrong place */
.site {
    color: #fff;
    }

/* Bad - opening brace missing space */
.site{
    color: #fff;
}
```

### Property Format

Each property must be on its own line and indented one level. There should be no space before the colon and one space after. All properties must end with a semicolon.

```css
/* Good */
.site {
    background-color: blue;
    color: red;
}

/* Bad - missing spaces after colons */
.site {
    background-color:blue;
    color:red;
}

/* Bad - missing last semicolon */
.site {
    background-color: blue;
    color:red
}
```

### Variables

```

```

### Indentation

Each indentation level is made up of four spaces. Do not use tabs. (Please set your editor to use four spaces)

```css
/* Good */
.site {
    color: #fff;
    background-color: #000;
}

/* Bad - all on one line */
.site {color: #fff; background-color: #000;}
```

Rules inside of `@media` must be indented an additional level.

```css
/* Good */
@media screen and (max-width:480px) {
   .site {
       color: green;
   }
}
```

### Brace Alignment

The opening brace should be on the same line as the last selector in the rule and should be preceded by a space. The closing brace should be on its own line after the last property and be indented to the same level as the line on which the opening brace is. Keep in mind that you are not saving bytes by this. Spaces are ultimately removed in the build process.

```css
/* Good */
.site {
    color: #fff;
}

/* Bad - closing brace is in the wrong place */
.site {
    color: #fff;
    }

/* Bad - opening brace missing space */
.site{
    color: #fff;
}
```

### Property Format

Each property must be on its own line and indented one level. There should be no space before the colon and one space after. All properties must end with a semicolon.

```css
/* Good */
.site {
    background-color: blue;
    color: red;
}

/* Bad - missing spaces after colons */
.site {
    background-color:blue;
    color:red;
}

/* Bad - missing last semicolon */
.site {
    background-color: blue;
    color:red
}
```

### Vendor-Prefixed Properties

When using vendor-prefixed properties, always use the standard property as well. The standard property must always come after all of the vendor-prefixed versions of the same property.

```css
.site {
    -moz-border-radius: 4px;
    -webkit-border-radius: 4px;
    border-radius: 4px;
}
```

If a vendor prefixed property is used, -moz, -webkit, -o, -ms vendor prefixes should also be used. Vendor-prefixed classes should align to the left with all other properties. Ideally, these would be moved to a mixin for maintainability.

```css
/* Good */
-webkit-border-radius: 4px;
-moz-border-radius: 4px;
border-radius: 4px;

/* Bad - colons aligned */
-webkit-border-radius:4px;
   -moz-border-radius:4px;
        border-radius:4px;
```

Do not use !important on CSS properties. They will ruin specificity.
```css
/* Good */
.site--red {
   color: red;
}

/* Bad - don't use !important */
.site {
   color: red !important;
}
```

### String Literals

Strings should always use double quotes (never single quotes).

```css
/* Good */
.container:after {
    content: "]";
}

/* Bad - single quotes */
.container:after {
    content: ']';
}
```

### Background Images and Other URLs

When using a url() value, always use quotes around the actual URL for readability. 

```css
/* Good */
.container {
    background: url("img/logo.png");
}

/* Bad - missing quotes */
.container {
    background: url(img/logo.png);
}
```

### Attribute values in selectors

Use double quotes around attribute selectors for readability.

```css
/* Good */
input[type="submit"] {
    ...
}

/* Bad - missing quotes */
input[type=submit] {
    ...
}

/* Bad - using single quote */
input[type='submit'] {
    ...
}
```


### Do not use units with zero values

Zero values do not require named units, omit the “px” or other unit.

```css
/* Good */
.container {
   margin: 0;
}

/* Bad - uses units */
.container {
   margin: 0px;
}
```

### Internet Explorer Hacks

NEEDS WORK

Only property hacks are allowed. To target Internet Explorer, use Internet Explorer-specific hacks like * and _ in the normal CSS files. Browser specific styles should not be in separate per-browser stylesheets. We prefer to keep all the CSS for a particular object in one place as it is more maintainable. In addition selector hacks should not be used. Classes like .ie6 increase specificity. Hacks should be kept within the CSS rule they affect and only property hacks should be used.

```css
/* Good */
.container {
   margin: 0;
   _margin: -1px;
}

/* Bad - uses selector hacks */
.container {
   margin: 0px;
}
.ie6 .container {
   margin: -1px;
}
```

### Class Qualification

Do not over-qualify class name selectors with an element type unless you are specifying exceptions to the default styling of a particular class.

``` css
/* Good */
.buttonAsLink {}

/* Bad - element name should be omitted */
span.buttonAsLink {}

/* Good - element is providing exceptions */
.buttonAsLink {}
span.buttonAsLink {}
```


### :hover and :focus

If :hover pseudo class is styled, :focus should also be styled for accessibility. Focus styles should never be removed.

```css
/* Good */
a:hover,
a:focus {
   color: green;
}

/* Bad - missing :focus */
a:hover {
   color: green;
}
```

### Avoid using IDs

Selectors should never use HTML element IDs. They are difficult to override with classes and will ruin our specificity. Always use classes for applying styles to specific areas of a page.

```css
/* Good */
.header {
   height: 100px;
}

/* Bad - using an ID */
#header {
   height: 100px;
}
```
