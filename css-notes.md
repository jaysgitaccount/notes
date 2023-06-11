# CSS Notes

Don't forget that the most specific selectors take priority.

## Object Oriented CSS

https://www.keycdn.com/blog/oocss

## Absolute units vs Relative

**Absolute** units are *the same in any context*, e.g. px. NOTE: px is the only absolute unit you should use for any web projects.

Using rem is great for responsive layouts, but you actually should use px in cetain cases, especially for things like padding and margins, which otherwise would scale up in a bloated-looking way. See: https://codyloyd.com/2021/css-units/

**Relative** units can *change based on their context*.

### `em` and `rem`

`em` and `rem` often used, and both refer to a **font size**. 

1em is the `font-size` of an element. So if an element's `font-size` is 16px, setting its width to 4em would make its width 64px (16 * 4 == 64).

1rem is the `font-size` of the *root* element (either `:root` or `html`). The math works the same as em, but without the added complexity of keeping track of the parents' font size. 

Relying on `em` means that a particular size could change if the context changes, which could be unwanted. e.g. in nested elements, 1.3em on each element means that the actual size of 1.3em increases by 1.3 with every level.

**NOTE**: using a relative size like `rem` to define font sizes is recommended! Users can change the base font-size to increase readability, and you can work with that.

### Viewport units

`vh` and `vw` relate to the size of the *viewport*. 1vh = 1% of the viewport height. 1vw = 1% of the viewport width.

When you want something to be sized relative to the viewport, e.g. full-height heroes, full-screen app-like interfaces, use these units.

#### `vmax` and `vmin`

`vmin`: A % of the viewport width or height, *whichever is smaller*. e.g. on a portrait viewport, 10vmin will resolve to 10% of the viewport width.

`vmax`: A % of the viewport width or height, *whichever is larger*. e.g. on a portrait viewport, 10vmax resolves to 10% of the viewport height.

NOTE: not available on Internet Explorer.

As these are derived from vh and vw, they can be used everywhere lengths are accepted, e.g. font-size, positioning, margins, padding, shadows, borders etc.

### Responsive Typography

**NOTE**: DO NOT use viewport units alone for scaling text. The size will reduce way too quickly.

You can combine a base size with more steady units (e.g. 16px) with a smaller viewport-relative adjustment (0.5vw), and do `math:calc(16px + 0.5vw)`.

The greater the viewport value is, the quicker the growth, so e.g. headers will grow quicker than the surrounding text. This can also be applied to line height:

    body {
        // font grows 1px per 100px of viewport width
        font-size: calc(16px + 1vw);
        // leading grows with font
        // with an additional 0.1em + 0.5px per 100px viewport
        line-height: calc(1.1em + 0.5vw);
    }

By using calc(base + vw), you can create responsive text WITHOUT the need for excessive media queries.

If you need to constrain the top-end for rapid-growth headings, you can use one single media query when the text gets too big.

    h1 {
        font-size: calc(1.2em + 3vw);
    }

    @media (min-width: 50em) {
        h1 {
            font-size: 50px;
        }
    }

### Full-Height Layouts

Declaring `body { height: 100vh; }` constrains your application to the height of the viewport. Make sure to apply `overflow` values on internal elements to the content isn't cut off.

You can also achieve this effect with flexbox or floats. Fix for iOS exception: https://github.com/Hiswe/vh-check#the-problem

### Sticky footers

    body {
        min-height: 100vh;
    }

This will make the footer stay at the bottom of the screen until it's pushed down by content.

### Responsive header section

    header {
        padding: 10vmin 1rem;
    }

In this, you'll have an auto resizing header for all screen sizes based solely on 10% of the vmin, plus 1rem so the header has some height to it. From https://css-tricks.com/simple-little-use-case-vmin/

### Fluid aspect ratios

Constraining the height:width ratio of an element is often useful, e.g. embedded videos. In some cases this can be achieved with viewport units.

If the video will be full-screen, you can set your height relative to the full width:

    .full-width {
        width: 100vw;
        height: calc(100vw * (9/16));
    }

If you're using a preprocessor like Sass, you can do this math there instead of in the browser.

If you need to constrain max-width, you can constrain max-height too:

    .full-width {
        width: 100vw;
        max-width: 30em;
        height: calc(100vw * (9/16));
        max-height: calc(30em * (9/16));
    }

### Breaking a container

When you want to, for example, mix constrained text with full-width backgrounds, such that it fills the viewport exactly:

.full-width {
    margin-left: calc(50% - 50vw);
    margin-right: calc(50%-50vw);
}

Further reading about breaking layouts for specific elements e.g. hero images https://cloudfour.com/thinks/breaking-out-with-viewport-units-and-calc/

### Scroll indicators

A bar that fills up as you scroll down the page. https://css-tricks.com/fun-viewport-units/#aa-getting-weird

## CSS Values

Every property used in CSS has a value type defining the set of values that are allowed for that property.

NOTE: CSS value types are typically written in documentation as `<>`, e.g. `<color>`, `<length>`. When you see the value type `<color>` listed for a property, that means you can use *any* valid color as a value for that property.

The term *value* in CSS refers to any expression supported by a value type that you choose to use.

### Percentages

If you set an element's font-size as a <length-percentage>, it will be a % of the *parent's* font-size. Same for width. Not every value accepts percentages.

### RGB vs RGBA

RBG: `background-color: rgb(2, 121, 139);` RGBA: `background-color: rgba(2, 121, 139, .3);`

Keep in mind when using A, it only affects the colour you are specifying, while opacity affects the entire element.

### String

Sometimes, e.g. when specifying generated content (see pseudo classes), you need to use a string.

    .box {
        width:400px;
        padding: 1em;
        border-radius: .5em;
        border: 5px solid rebeccapurple;
        background-color: lightblue;
    }

    .box::after {
        content: "This is a string. I know because it is quoted in the CSS."
    }

### Functions
CSS has functions in the form of property values, e.g. rgb(), hsl(), url().

The `calc()` function does simple calculations inside CSS, and is useful for values that need to be defined at runtime.

    .wrapper {
        width: 400px;
    }

    .box {
        width: calc(20% + 100px);
    }
    

## Pseudo classes and pseudo elements

The difference between pseudo-elements and pseudo-classes: 
- Pseudo-classes are prefixed with *one* colon and are a way to target elements that *already exist in HTML*.
- Pseudo-elements have *two* colons and are used to target elements that *don't normally exist* in the markup.

A **pseudo-class* is a selector that selects elements *that are in a specific state*, e.g. the first element of its type, or being hovered over.

To make the first paragraph on a page bold, you could

.first {
    font-size: 120%;
    font-weight: bold;
}

and manually apply `<p class="first"></p>`.

OR,

You could just not do that (for many reasons), and instead use `:first-child` pseudo-class selector. Below, this will always target the first child `p` element of an article.

    article p:first-child {
        font-size: 120%;
        font-weight: bold;
    }

All pseudo classes target some part of the document in a certain state and behave as if you've added a class. Other examples are `:last-child`, `:only-child` (an element without any siblings), `:invalid` (any form, fieldset, input or other form element whose contents fail to validate). 

### User action pseudo classes

Dynamic pseudo classes that only apply when the user interacts with the document in some way.

- `:hover` applies if the mouse pointer is over an element.
- `:focus` applies if the user *focuses* the element by clicking or using keyboard controls.

    a:link, a:visited {
        color: purple;
        font-weight: bold;
    }
    
    a:hover {
        color: hotpink;
    }

### Pseudo-element

These act as if you have added a whole new HTML element into the markup. They start with double colon `::`, as in `::pseudo-element-name` (older code may use single colon).

    article p::first-line {
        font-size: 110%;
        font-weight: bold;
    }

This will always affect the words on the first line of an article, regardless of width/size changes. Basically it acts as if a `<span></span` was wrapped around the first formatted line, but it is dynamic. Due to this behaviour it is considered a pseudo *element*.

### Combining pseudo classes and elements

You can chain these selectors, e.g. to make the first line of the first paragraph of any article bold, you would do:

    article p:first-child::first-line {
        font-size: 120%;
        font-weight: bold;
    }

## Generating content with ::before and ::after

Special pseudo elements that are used along with the `content` property to insert content via CSS.

    .box::before {
        content: "This will be before the other content. "
    }
HTML:
    <p class="box">Content in the box in my HTML page.</p>
    

In above, the ::before sentence will appear before any text in the p.

However, inserting strings is not commonly done as it is inaccessible to some screen readers and also is hard to find and edit.

A more common use is to insert an icon, e.g. an arrow that is a visual indication that shouldn't be read by a screen reader. 

    .box::after {
        content: " ➥"
    }

OR, to insert an empty string that can then be styled.

    .box::before {
        content: "";
        display: block;
        width: 100px;
        height: 100px;
        background-color: rebeccapurple;
        border: 1px solid black;
    } 

This code will give you a 100x100 purple square in the line above the p.

### Generated Content

The use of ::before and ::after along with the `content` property is referred to as generated content in CSS. You will often see this being used for various tasks. 

For example, at https://cssarrowplease.com/, you can generate an arrow with CSS that can be stuck onto a box, like a speech bubble pop up,

https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Selectors/Pseudo-classes_and_pseudo-elements#generating_content_with_before_and_after

## Fonts

You should always have backup fonts. A popular fallback font stack:

    body {
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
    }

If you're going for a neutral font style, something like this (looking for the default font of the system's UI) is viable.

Otherwise, font libraries are an option.

### Text styles

    h1 {
        font-style: italic;
    }

For cosmetic only italics, bold, underlining, highlight etc... you should use `font-style`. HTML tags like `<em></em>` are for text that should have some kind of semantic emphasis (that needs to be detected by screen readers).

e.g. for headers that are italic, use font-style.

    h1 {
        font-family: sans-serif;
        margin: 0;
        font-size: 38px;
    }

    .wide {
        font-size: 24px;
        letter-spacing: .5em;
    }

`letter-spacing` is for adjusting space between words, and can be cute in headers.

`line-height` can increase readability, and can also be dynamically adjusted along with font-size so the end result oesn't look weird.

`text-transform` changes the case of the text, e.g. all uppercase, or capitalising every word.

`text-shadow` adds shadow. Usage: `text-shadow: offset-x offset-y blur-radius color;`

#### Truncate string with ellipsis

Useful trick: with `text-overflow`, you can truncate overflowing text with an ellipsis `...`. You need a couple over properties because the default behaviour of text printing outside its container is NOT considered `overflow`.

    .overflowing {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

Reference: https://css-tricks.com/snippets/css/truncate-string-with-ellipsis/

## Commonly used CSS properties

Mostly shorthands. Many properties that make up these shorthands

`background`: https://developer.mozilla.org/en-US/docs/Web/CSS/background

`border: width style color` (e.g. border: medium dashed green, border: solid black 2px) and `border-radius: top-left top-right bottom-right bottom-left`. Pass a single value into border-radius to make circular corners, or 2 values to make elliptical corners.

`box-shadow: offset-x offset-y blur-radius spread-radius color`, e.g. `border radius: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);` 
There's also the `inset` value where, if not specified, the shadow is assumed to be a drop shadow. Adding `inset` changes the shadow to one inside the frame (like debossing). Inset shadows are drawn inside the border (even transparent omes), above the background, but below content.

`overflow`: defines what happens when the contents of an element are too big to fit. Common use: adding scrollbars to an element inside a webpage, e.g. a `card` style element with scrollable content.
The `overflow` property is a shorthand for `overflow-x` and `overflow-y`, so if 2 keywords are specified, the first will be for x and the second for y. Example values:

- visible: content rendered outside padding box
- hidden: content clipped, no scrollbars, no scrolling support for the user. Content can still be scrolled via code.
- clip: content clipped, all scrolling forbidden.
- scroll: content clipped if needed, scrollbar always displayed whether or not it's needed. Printers may still print overflowing content.
- auto: depends on user agent. If content fits inside, it looks like `visible`, but still establishes new block formatting context. Desktop browsers provide scrollbars if there is overflow.
- overlay: same as `auto`, but scrollbars drawn on top of content instead of taking up space.

`opacity` takes an alpha value from 0 to 1, affecting the alpha of the entire element. If opacity is not 1, the element is added to a new *stacking context*.

## Advanced Selectors

Useful when you can't or don't want to change your HTML markup. There are many advanced selectors, but I will note the most common/useful ones.

### Accessing elements without referring to classes

Glossary of complex CSS selectors: https://learn.shayhowe.com/advanced-html-css/complex-selectors/

#### Parent and Sibling Combinators

- `>` the child combinator
- `+` adjacent sibling combinator
- `~` general sibling combinator, gets all the siblings whereas + only gets the first one

    <main class="parent">
        <div class="child group1">
            <div class="grand-child group1"></div>
        </div>
        <div class="child group2">
            <div class="grand-child group2"></div>
        </div>
        <div class="child group3">
            <div class="grand-child group3"></div>
        </div>
    </main>

If we wanted to select all the descendants of `main`, we would use

    main div {
        /* CSS */
    }

But if you want to select *only* the child or grand-child divs, you would use `>`, which only selects direct children.

    main > div {
        /* Only direct children */
    }

    main > div > div {
        /* Only the grand-children of main */
    }

To select an element that is *adjacent* to the target/on the same level of indentation, use `+`.

    .group1 + div {
        /* Only the div .group2 is selected
    }

    .group1 + div + div {
        /* Only the div .group3 is selected */
    }

To select *all* an element's siblings, use `~`.

    .group ~ div {
        /* All of group1's siblings selected */
    }

I wonder if these selectors are inclusive.

#### Pseudo-selectors

Some are based on position/structure, others on states or interactions. They have the same specificity as regular classes and can be chained.

#### Dynamic and User Action pseudo-classes

These will make your page feel much more dynamic and interactive.

- `:focus` - an element that is currently selected by the user's cursor or keyboard (e.g. input field with click or tab)
- `:hover` - applies to anything under the user's pointer, e.g. highlighting interactive buttons and links
- `:active` - applies to elements currently being clicked, especially useful for giving feedback that their action had an effect. For a tactile feel.

As an example, here is the default styling for links (blue and underlined, purple when clicked).

    a { /* All links */
        text-decoration: underline;
    }

    a:link { /* Unvisited state */
        color: blue;
    }

    a:visited { /* Visited state */
        color: purple;
    }

#### Structural pseudo-classes

Select elements based on their position within the DOM.

- `:root` is a special class that represents the very top level of your document. Generally (but not always) equivalent to the `html` element. This is where you would want your 'global' CSS rules, e.g. custom properties and CSS variables, or rules like `box-sizing: border-box;`
- `:first-child` and `:last-child`
- `:empty` - elements with no children at all. 
- `:only-child` - elements that don't have any siblings.

For a more dynamic approach, use `:nth-child`, a flexible pseudo-class with some different uses.

    .myList:nth-child(5) { /* selects 5th element with this class */ }

    .myList:nth-child(3n) {/* selects every 3rd element with class */}

    .myList:nth-child(3n + 3) {/* selects every 3rd element with class myList, STARTING WITH the third */}

    .myList:nth-child(even) {/* selects every even element with class myList */}

#### Pseudo-elements

Pseudo-elements are more abstract, allowing us to interact with parts of the HTML that aren't elements at all. Same specificity as regular elements.

- `::marker` - customise the styling of your `<li>` elements' bullets or numbers.
- `::first-letter` and `::first-line`
- `::selection` changes the highlighting when the user selects text on the page.
- `::before` and `::after` - often used to decorate text on the page.
- `::file-selector-button` represents the button of an `<input>` of `type="file"` Example:

    input {
        margin-top: 1rem;
    }

    input::file-selector-button {
        font-weight: bold;
        // etc
    }

All pseudo-elements: https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements

#### Attribute Selectors

Since we write our own attribute values, we need a slightly more flexible system.

- `[attribute]` - general selector that selects anything where the given attribute exists
- `selector[attribute]` - combine the attribute selector with other types of selectors, e.g. `.class[attr]` or `#id[attr]` or `img[src]`
- `[attribute="value"]` - you can use `=` to match a specific attribute with a specific value. e.g. `img[src="puppy.jpg"]`

Sometimes you need more general uses though, like selecting only `img` with `src` ending in `.jpg`. These attributes use a similar syntax to *regular expressions*.

- `[attribute^="value"]` - `^=` matches strings from the start. e.g. `[class^='aus']` will target any class beginning in 'aus', such as `class='austria'` and `class='australia'`.
- `[src$='.jpg']` - `$=` targets matches from the end.
- `[attribute*='ill']` - `*=` matches an attribute that contains the value anywhere. so `for='bill'`, `for='silly'` will both match.
- `[attribute operator value i]` - adding `i` or `I` before the closing bracket causes the value to NOT be case sensitive (within the ASCII range). e.g. `[src*='puppy' i]` will match any src containing `puppy` e.g. `brownpuPPY`, `Puppysitting`
- similarly, adding `s` or `S` before the close bracket causes the characters to be compared WITH case sensitivity.

Full list of attribute selectors: https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors

## Position

- `static`: the default `position` value, where the elements follow the document flow of the HTML
- `relative`: works almost the same as static, BUT allows you to change the top/right/bottom/left position. Takes the element OUT of the document flow, so it does not disrupt the positioning of any elements around it. But you don't really use relative with top/right/bottom/left.
- `absolute`: DOES get used with top/right/bottom/left. The document flow completely ignores the element with `absolute`, and everything else is rendered *as if the element doesn't exist at all*. Setting top/left/bottom/right will position the element relative to the parent *only if* the parent's position is NOT `static`. So the most common use of `position: relative` is to enable an element to have its child `position: absolute` over it. Otherwise, the `absolute` element will fall back to the next element whose position is not `static`, or just to the `html` element.
- `fixed`: always positioned relative to the ENTIRE HTML document. AND, it stays in position on the screen while scrolling.
- `sticky`: basically a combination of `relative` and `fixed`. A `sticky` element starts off as if it was `position: relative`. As soon as the element reaches the top of the page, it becomes `fixed` in behaviour. So if you had `position: sticky; top: 0;` as soon as it would have left the page, it sticks to the top of the page as you scroll.

## CSS Functions

CSS does not allow you to create your own functions. Instead, it comes bundles with set functions that help you solve the most common styling problems.

When thinking about responsive design:

### calc()

Most powerful use cases: mixing units, ability to nest `calc ( calc () - calc () )`

    :root {
        --header: 3rem;
        --footer: 40px;
        --main: calc(100vh - calc(var(--header) + var(--footer)));
    }

In this example, calc is doing `main = 100vh - (header + footer)`. mixing vh, rem and px units.

By the way, `--header`, `--footer`, `--main` are CSS variables. Combining `calc()` with CSS variables saves a lot of repeating.

NOTE: this is not necessariy the best use of calc in layouts, just an example.

### min()

    #iconHolder {
        width: min(150px, 100%);
        height: min(150px, 100%);
        box-sizing: border-box;
        border: 6px solid blue;
    }

`min()` is great for responsive websites. In this example, `width: min(150px, 100%);`: if there are 150px available to the image, it will take up all 150. If not, it will switch to 100% of the parent's width. Same for height. `min()` sets the *maximum* allowed value.

You can do basic math inside min, e.g. `width: min(80ch, 100vw - 2rem);`

### max()

`max()` works the same way as min, but in reverse: it will select the *largest* possible value from within the parentheses. So max ensures a *minimum* allowed value for a property.

e.g. `width: max(100px, 4em, 50%);` From this list of given sizes, the largest will be selected. As long as 4em or 50% results are longer than 100px, `max()` will select the larger one of them. Width will not ever be less than 100px.

It is most useful when the viewing window is either exceptionally small, or the user increases content size by using the browser's zoom feature. It's not the most used, but it may be handy for accessibility.

### clamp()

Great for fluid/responsive elements, especially typography. It takes 3 values:

    h1 {
        font-size: clamp(320px, 80vw, 60rem);
    }

1. smallest value (320px)
2. ideal value (80vw)
3. largest value (60rem)

### Basically

For `min()` and `max()`, you provide an argument list of values, and the browser calculates which is either the smallest or largest respectively, and uses that.

For `clamp()`, you enter a minimum value, an ideal value (from which to calculate), and a maximum value.

These can be used anywhere they make sense, e.g. where <length>, <frequency>, <time>, <percentage>, <number>, <integer> are allowed. They can be used with `calc()`, AND you can remove the call to calc.

e.g. `font-size: max(calc(0.5vw - 1em), 2rem)` is the same as `font-size: max(0.5vw - 1em, 2rem)`.

Practical uses of CSS math functions: https://moderncss.dev/practical-uses-of-css-math-functions-calc-clamp-min-max/

## Custom Properties (CSS Variables)

Reference a CS value whenever you want! No more manually updating all instances of a value. You can also redefine custom properties under different contexts, which is really helpful for creating themes e.g. dark and light themes for websites.

### Declaring Custom Properties

    .error-modal {
        --color-error-text: red;
        --modal-border: 1px solid black;
        --modal-font-size: calc(2rem + 5vw);

        color: var(--color-error-text);
        border: var(--modal-border);
        font-size: var(--modal-font-size);
    }

First, you declare the variable using a `--` followed by case sensitive kebab-case. (`--color` is different from `--Color`).

To access a custom property, use `var()` as the value of a CSS property, then place the variable name in the brackets.

**Fallback values**: `var()` takes 2 parameters, the second is an optional fallback just in case the custom property is invalid or undeclared. You can also pass in ANOTHER value that has its own fallback!

    .fallback {
        --color-text: white;

        background-color: var(--undeclared-property, black);
        color: var(--undeclared-again, var(--color-text, yellow));
    }

### Scope

In the above example, the variables were declared and then accessed in the same block. That's because the scope of a custom property is determined by the selector. The scope includes the selector the custom property was declared for, *as well as any descendants* of the selector. 
HTML:
    <div class='cool-div'>
        <p class='cool-paragraph'>Check out my cool, red background!</p>
    </div>

    <p class='boring-paragraph'>I'm not in scope so I'm not cool.</p>
CSS:
    .cool-div {
        --main-bg: red;
    }

    .cool-paragraph {
        background-color: var(--main-bg);
    }

    .boring-paragraph {
        background-color: var(--main-bg);
    }

In this example, only `cool-paragraph` would get the red background, since it's a descendant of cool-div.

Custom properties are ONLY accessible for the matching selector and its descendants, just like normal CSS. So you can't retrieve values through sibling selectors or anything.

### `:root`

Sometimes you'll want to limit the scope of a variable. But if you want to use a variable on many unrelated selectors, you should put it on the `root` selector, which is like the `html` selector except it has a higher specificity.

### Creating themes with custom properties

Add a class attribute to the `html` element so the page has a default theme. Next in the CSS, create 2 scopes on `:root`, `:root.dark` and `:root.light` with custom variables on them. You can use JS to toggle themes. OR,

### Media Queries

`@media` checks conditions based on the user's device. So for seeing if a user prefers a light or dark theme you can set it up like this:

    :root {
        --border-btn: 1px solid rgb(36, 36, 36);
        --color-base-bg: rgb(240, 240, 240);
        --color-base-text: rgb(18, 18, 18);
        --color-btn-bg: rgb(220, 220, 220);
        --theme-name: "light";
    }

    @media (prefers-color-scheme: dark) {
        :root {
            --border-btn: 1px solid rgb(220, 220, 220);
            --color-base-bg: rgb(18, 18, 18);
            --color-base-text: rgb(240, 240, 240);
            --color-btn-bg: rgb(36, 36, 36);
            --theme-name: "dark";
        }
    }

Note: for this particular media query, only `dark` and `light` values are valid. Also, the default value for no preference in this example is light.

### Handling invalid custom properties

When an invalid `var()` value is encountered, the browser will substitute the initial or inherited value. As in:

If a normal (but not custom) value is found to be invalid (e.g. color: 16px;), the browser will discard the rule as if it does not exist, and then render the result of that. 

With invalid custom properties, the browser will first check if that property (e.g. color) is inheritable, and check if any parents have that property set. If not, then it will be **set to its default initial value**. So even if a color was declared for that same element further up the stylesheet, it will not be read and applied.

### Custom Properties in JavaScript

To use custom properties, it's just like standard properties:

    // get inline
    element.style.getPropertyValue("--my-var");

    // get variable from wherever
    getComputedStyle(element).getPropertyValue("--my-var");

    // set variable on inline style
    element.style.setProperty("--my-var", jsVar + 4);

## Browser Compatibility

Just because something is new doesn't mean it's a great idea to use it immediately. It takes time for new features to be supported by all common browsers. Use https://caniuse.com/ to check if new features are supported.

### On Mobile

On iOS and iPadOS, Safari is the only "full" supported browser, as in even other browsers (e.g. Chrome and Firefox) are using Webkit, the Safari rendering engine. So for your web app to work for Apple users, you need to support Webkit and other Safari technologies.

Also keep in mind the various screen sizes of mobile devices. Emulation is not a perfect way of recreating the actual experience on the device (you won't be able to test for the specific considerations of the OS).

## CSS Animations

### Transition
For simple animation effects. Have the end state on the pseudo class or whatever you're using.

Put the `transition` property on the **base** class always: otherwise when the state that you're using to trigger the animation is no longer applied, the animation will just snap back to its original value.

`transition` compulsory parameters: CSS property you want to add an effect to, and the duration (ms, s etc). If the duration isn't specified, default is 0.

The `transition-timing-function` specifies the speed CURVE of the effect. Possible values: 
- `linear` - no easing
- `ease` - slow start, fast, end slow
- `ease-in` - specifies a transition effect with a slow start
- `ease-out` - specifies a transition effect with a slow end
- `ease-in-out` - specifies a transition effect with a slow start and end
- `cubic-bezier(n,n,n,n)` - lets you define your own values in a cubic-bezier function, so you can add effects like exaggerated anticipation You can also examine the curve in the Inspect window.

`transition-delay`: self explanatory, takes a value in seconds.

### Animation

The `animation` property takes the same arguments as `transition`, BUT instead of specifying a property you want to animate, you just name the animation. Then,use `@keyframes animation-name { }` (external to the CSS class) to link them together. Keyframes take values from when the animation is `0%` to `100%` complete.

There is another value taken by `animation`, `animation-fill-mode`, which takes `backwards` or `forwards` or both. Backwards: apply all the properties at the 0% keyframe. Likewise for forwards, but 100%. So for `forwards`, after the animation is done, it will stay as is instead of snapping back.

Example given which makes a cube move inside another cube in a circle:

    @keyframes left-to-right {
        0% {
            transform: translateX(0);
        }
        33% {
            transform: translateY(100%);
        }
        66% {
            transform: translateX(100%) translateY(100%);
        }
        100% {
            transform: translateX(100%);
        }  
    }

Animation iteration count: `animation: left-to-right 1s ease-in forwards 3` the 3 value is for number of times the animation will loop. Instead of a number you can also pass `infinite` to make it loop forever.

Adding `alternate` at the end will make the animation go back and forth between keyframes on each play.

`animation-play-state` takes `running` (default) or `paused`, so you can do something like on hover, pause animation.

### Performance Considerations

You can see how heavy your page is in the `Performance` tab. By throttling your CPU you'll be able to see the performance of your page on lower end devices. You can record performance data of your page in action and receive a detailed report.

Don't overuse animations and try not to animate anything that isn't `opacity` or `transform`.

When an element changes **that could possibly change elements around it** you have to re-layout everything on your page. e.g. animating an element's margin will move everything else too. So it's significantly heavier than just doing `transform`.

## Clips and Masks

Masking defines how to use an image or graphical element as a luminance or alpha mask.

Apply the property `mask-image: url(imageurlhere.png)` with a link to the alpha map (black=visible, white=hidden). 

### SVG mask
If your mask has a hard edge, it's recommended to use SVG insteasd of transparent PNG as it will be smaller in size. (You can do this in photoshop) 
If your mask has a soft edge, e.g. clouds, you'll need to use a PNG.

    .mask {
        mask: url("mask.png");
        -webkit-mask: url("mask.png");
        mask-size: cover;
        -webkit-mask-size: cover;
        width: 100%;
        height: 100%;
    }

### Using shapes

From: https://hacks.mozilla.org/2017/06/css-shapes-clipping-and-masking/
Using `clip-path`, Firefox allows you to define shapes within your stylesheet so there's no need for an SVG: `circle`, `ellipse`, `inset`, `polygon`.
You can also animate these with CSS. BUT, you can't mix function shapes, e.g. morphing from a circle to an inset. Also when animating polygons, the polygons must preserve the same number of vertices during the whole animation.

    .circle {
        animation: 2s infinite alternate circle;
    }

    @keyframes circle {
    from {
        -webkit-clip-path: circle(0%);
        clip-path: circle(0%);
    }
    to {
        -webkit-clip-path: circle(120px);
        clip-path: circle(120px);
    }
    }

    body {
        display: flex;
        align-items: center;
        justify-content: center;
        min-height: 90vh;
        box-sizing: border-box;
    }

    body > * {
        display: block;
        flex: 0 0 auto;
    }

You can also mask an image according to user input (e.g. periscope effect following the mouse) with JS.

### Clipping vs Masking

Masking modifies the pixels of an image, changing their value. It does not alter interaction with the image itself.

Clipping "cuts" the element, including its collision surface. Only visible parts can be interacted with.

### Masking an element using its parent

You can mask all children of an element, such that they are only visible when inside the parent element, using `overflow: hidden`. 

### Support Fallbacks

    @supports not selector(:focus-visible) {
        .button.with-fallback:focus {
            /* Fallback for browsers that don't support focus-visible */
            outline: 3px solid deepskyblue;
            outline-offset: 3px;
        }
    }

- `:focus` - an element that has received focus. Usually triggered when the user clicks or taps of an element, or selects it with TAB. Only applies to the focused element itself. Focus can be triggered via keyboard (tabbed), mouse click, touch screen tap or being navigated to by any other means including through JavaScript `document.querySelector("input").focus();`
- `:focus-within` - if an element or any of its descendants matches the `:focus` state. Can be used to affect a parent element based on the existence/state of child elements e.g. changing the colour of a form when an input is focused.
- `:focus-visible` - when an element matches the `:focus` state and the User Agent determines that the focus should be made evident on the element. Normally used to differentiate user input from a keyboard vs a mouse. For older browsers, you may need to repeat styling for this rule within `:focus` as a fallback. However, most browsers' native styling is an outline which is fine.

## CSS Grid

Define container (the direct parent of grid items) with `display: grid`.
Set column sizes with `grid-template-columns` and rows with `grid-template-rows`.
Then, place child elements into the grid with `grid-column` and `grid-row`.

Source order of grid items doesn't matter. You can easily rearrange with media queries.

**Grid lines**: dividing lines that make up the structure of the grid, on either side of a row or column.

**Grid track**: the space between 2 adjacent grid lines, in other words the actual columns or rows of the grid.

**Grid area**: total space surrounded by 4 grid lines; can be composed of any number of grid cells.

**Grid item**: direct descendant of grid container.

**Grid cell** the single space between two adjacent row/column grid lines, in other words 1 cell.

### Grid Properties

#### Parent (Grid Container)

**display**
- grid: block level grid
- inlin-grid: inline level grid

**grid-template-columns/rows** - represents track size. Space between the tracks represents grid lines.
- track-size: length/%/fraction of the free space in the grid using the `fr` unit.
- line-name: a name of your choosing. Grid lines are automatically assigned positive numbers along the xy axes starting from the top left, and negative numbers from the opposite corner. BUT you can explicitly name the lines with `[]`

    .container {
        grid-template-columns: [first] 40px [second line2] 50px [line3] auto [col4-start] 50px [five] 40px [end]
        grid-template-row: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
    }

NOTE: A line can have multiple names. See `second` and `line2`.
- repeat: If your definition contains repeating parts:
    grid-template-columns: repeat(3, 20px [col-start]);
This is the equivalent of repeating `20px [col-start]` three times.

**grid-template-areas** - defines a grid template by referencing the names of the grid areas which are specified with `grid-area`. Repeating the name of a grid area causes the content to span those cells. `.` is an empty cell. Essentially a text visualisation of the grid.
- grid-area-name: the name of a grid area cell

    .container {
        display: grid;
        grid-template-columns: 50px 50px 50px 50px;
        grid-template-rows: auto;
        grid-template-areas: 
            "header header header header"
            "main main . sidebar"
            "footer footer footer footer"
    }

This creates a grid that's 4x3. Entire top row is the header, middle row first half is main, then an empty cell, then sidebar. Last row is all footer.
Each row needs to have the same number of cells.
NOTE: You aren't naming lines with this syntax, but areas.

**grid-template** - shorthand for `grid-template-rows`, `grid-template-columns`, `grid-template-areas`.
- none: setting `none` makes all properties their initial values

Simple syntax:
    .container {
        grid-template: none | <grid-template-rows> / <grid-template columns>;
    }
More complex but useful syntax:
    .container {
        grid-template: 
        [row1-start] "header header header" 25px [row1-end]
        [row2-start] "footer footer footer" 25px [row2-end]
        / auto 50px auto
    }

This is the same as
    grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
    grid-template-columns: auto 50px auto
    grid-template-areas:
        "header header header"
        "footer footer footer";
    
    Since `grid-template` doesn't reset the implicit (auto) grid properties, which is a common need, it's recommended to use `grid` instead of `grid-template`.

**column-gap**, **row-gap**, **grid-column-gap**, **grid-row-gap** - specifies the size of the grid lines/gutters.

    .container {
        grid-template-columns: 100px 50px 100px;
        grid-template-rows: 80px auto 80px; 
        column-gap: 10px;
        row-gap: 15px;
    }

These only apply *between* the columns/rows, not the outer edges.

**gap** - shorthand for row/column-gap
    gap: 15px 10px;

**justify-items** - aligns along the inline row
**align-items** - aligns along the block row
- start
- end
- center
- stretch

**place-items** - sets both align-items and justify-items. First value: `align-items`, second is `justify-value`. If only one value, it's applied to both.

For when the total size of your grid is less than the size of the parent container, you can align the grid *within* its parent.
**justify-content** - set the alignment of the grid within the grid container, to be along the inline (row) axis.
**align-content** - align upon the block (column) axis.
- start
- end
- center
- stretch
- space-around
- space-between
- space-evenly

**place-content** - sets both justify/align content similarly to place-items.

**grid-auto-columns**, **grid-auto-columns** - specifies the size of any auto-generated (implicit) grid tracks, which are created when there are more grid items than cells in the grid, or when a grid item is placed outside the explicit grid.

**grid-auto-flow** - controls how the auto placement algorithm (if you have grid items that aren't explicitly placed on the grid) works
- row
- column
- dense: attempt to fill in holes earlier in the grid if smaller items come up later. Note that dense only changes the visual order of your items, so it's bad for accessibility.

#### `grid` shorthand
Sets, in a single declaration: grid-template-rows, grid-template-columns, grid-template-areas, grid-auto-rows, grid-auto-columns, and grid-auto-flow. (You set BOTH the explicit and implicit properties in one grid declaration).

Examples of equivalent code blocks:

    .container {
        grid: 100px 300px / 3fr 1fr;
    }
    .container {
        grid-template-rows: 100px 300px;
        grid-template-columns: 3fr 1fr;
    }

and

    .container {
        grid: auto-flow dense 100px / 1fr 2fr;
    }
    .container {
        grid-auto-flow: row dense;
        grid-auto-rows: 100px;
        grid-template-columns: 1fr 2fr;
    }

Complex but handy syntax: you specify grid-template-areas, grid-template-rows, grid-template-rows, grid-template-columns, and everything else is set to their intial values.

    .container {
        grid: 
            [row1-start] "header header header" 1fr [row1-end]
            [row2-start] "footer footer footer" 25px [row2-end]
            / auto 50px auto;
    }

Which is equivalent to:

    .container {
        grid-template-areas:
            "header header header"
            "footer footer footer";
        grid-template-rows: [row1-start] 1fr [row1-end row2-start] 25px [row2-end];
        grid-template-columns: auto 50px auto;
    }

### Grid Items

The following properties don't work on grid items:
- float
- display: inline-block;
- display: table-cell
- vertical-align
- column

### `fr` unit
Flexible unit for Grid: a fractional unit. 1fr is 1 part of the available space.

    .container {
        display: grid;

        grid-template-columns: 1fr 1fr 1fr 1fr;
        grid-template-rows: 100px 200px 100px;

        grid-template-areas: 
        "head head2 . side"
        "main main2 . side"
        "footer footer footer footer"
    }

`head` and `head2` (same with `main`) create individual cells at those locations with those names.
`.` makes an empty cell.
The 4 footers will make the entire bottom line one continuous row of the footer. Similarly, the `side` values will make the top 2 cells on the right one conjoined cell vertically.

`fr` can be mixed with other values. The `fr` values will be divided between the space that's left AFTER other fixed units are calculated.

    main {
        width: 800px;
        display: grid;
        grid-template-columns: 300px 10% 1fr 1fr;
        /* 300px 80px 210px 210px */

        grid-template-rows: auto;
    }

## Making content stretch to fill page

From [this question](https://stackoverflow.com/questions/6654958/make-body-have-100-of-the-browser-height), if I want to have `body` fill the height and width of the viewport so that I can nicely center content:

    html, body {
        height: 100vh;
        width: 100vw;
    }

You need to set this on the `html` element as well, because `body` looks to its parent for guidance on sizing.

## CSS gradients

Basic syntax: `background-image: linear-gradient(<topcolor>, <bottomcolor>);`. This is a top to bottom gradient (red to yellow). There are other configs available by specifying the horizontal and vertical starting positions.

e.g. this is a top left to bottom right red to yellow gradient:

    background-image: linear-gradient(to bottom right, red, yellow)

Multiple color stops (red to yellow to green gradient):

    background-image: linear-gradient(red, yellow, green);

## `box-sixing: border-box;`

https://css-tricks.com/almanac/properties/b/box-sizing/

`box-sixing: border-box;` **includes** an element's border and padding in the total size of the element. If a div has width 600, padding 5px, and border width 5px, then the width of the inner **content box** of the div is actually 590px. The total width of the div, MINUS margins, will be 600px; 

`box-sixing: content-box;` is the default CSS behaviour, where if a child's width is 100% of the parent's width, adding any border or padding to the child will make it extend outside it's parent's width by that many pixels.

## [Collapsing margins](https://www.w3.org/TR/CSS2/box.html#collapsing-margins)

## Widths, Heights, and box sizing

You can access these properties of a HTMLDomElement in JS, e.g. `element.clientWidth`. These are read-only properties and you can't set them via CSS or JS.

`clientWidth`: the "inner width", including padding ONLY, and excluding borders and scrollbars. For inline elements and elements with no CSS, this is 0. When clientWidth is used on the root element, the viewport's width (excluding any scrollbars) is returned.

`offsetWidth`: outer width, the space occupied by the element, including borders and padding.

`scrollWidth`: the total width of the element, *including* content that is currently offscreen that is only visible if you scroll.

### Moving an element relative to its parent

The `offsetParent` of an element is its closest parent that has a `position` of `absolute` or `relative`. You need to set this manually if you're not using `<body>`!

Then, you can simply call `element.offsetLeft`, etc. to get the element's offset values relative to its parent, instead of to the window.

This should make your JS animation responsive to the parent changing transforms.