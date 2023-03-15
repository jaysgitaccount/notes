# HTML Notes

## Emmet

### Shortcuts

- HTML boilerplate - `SHIFT + 1 -> ENTER`
- Wrap with Abbreviation - `CTRL + SHIFT + A` - this has many interesting uses, like using CSS selectors to create many descendants at once
- Remove tag - `ALT + SHIFT + A`


## DOM
The Document Object Model is a REPRESENTATION Of the contents of a web page.

To target the DOM with JS, you use CSS selectors or relationship properties to target the nodes you want.

CSS selectors for `<div class="display"></div>`:

- `div.display`
- `.display`
- `#container > .display`
- `div#container > div.display`

Relationship selectors like `firstElementChild`, `lastElementChild`:

    const container = document.querySelector('#container');
    // selectes first div with id container

    console.dir(container.firstElementChild);
    // selects first child of #container

    const controls = document.querySelector('.controls');
    // selects the .controls div

    console.dir(controls.previousElementSibling);
    // selects the prior sibling

## DOM methods

The HTML is parsed by the browser and converted to the DOM. 

### Query Selector
The differentiating factor between query selectors, other than usage case, is performance benefits.

`element.querySelector(selector)` returns a reference to the FIRST match.
`element.querySelectorAll(selectors)` returns a *nodelist*.

When using `querySelectorAll`, the return value is NOT an array, but a *nodelist*. As in, several array methods cannot be applied to nodelists. But you can convert it into an array using `Array.from()` or the `spread` operator. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax

### Element Creation
`document.createElement(tagName, [other parameters])` creates a new element of tag type tagName. e.g. `const div = document.createElement('div');`

NOTE: This does not put your new element into the DOM, it just creates it in memory. This is so you can manipulate the element before putting it on the page.

Place the element into the DOM using:
- `parentNode.appendChild(childNode)`, as the last child of parentNode
- `parentNode.insertBefore(newNode, referenceNode)`, inserts newNode into parentNode before referenceNode

### Remove Elements
- `parentNode.removeChild(child)` removes child from parentNode on the Dom and returns a reference to child

### Altering Elements
When you use a reference to style a node `div.style.color = 'blue';`, it is considered an inline style.

NOTE: If you'e accessing a kebab case CSS rule with JS, you'll need to use camelCase `div.style.cssText = background-color: white;` or bracket notation `div.style[background-color]` instead of dot notation. 

### Editing Attributes
`div.setAttribute('id', 'theDiv');` - if id exists on div reference, update it to 'theDiv', else create an id with value "theDiv"
`div.getAttribute('id');` - returns value of specified attribute
`div.removeAttribute('id')` - removes specified attribute

### Working with classes
It is standard to toggle a CSS style rather than adding or removing inline CSS.
`div.classList.add('new');` - adds class "new" to your new div
`div.classList.remove('new');` - removes class "new"
`div.classList.toggle('active');` - if div doesn't have class "active" then add it, if it does then remove it

### Text Content
`div.textContent = "Hello World!";` creates a text node containing Hello World and inserts into div.

### HTML Content
`div.innerHTML = '<span>Hello World!</span>';` renders the HTML inside div

## When the script is run

NOTE: The JS does not alter your HTML, but the DOM.

The JS is run when the JS file is run, or when the script tag is encountered in the HTML. As a result, many DOM manipulation methods will not work if the JS is in the `<head>`, because the code is being run BEFORE the nodes are created in the DOM.

The easiest way to fix this is to include your JS at the bottom of the HTML so it runs after the DOM nodes are parsed and created.

Alternatively, link the JS file in `<head>` using `script src="script.js" defer></script>` The `defer` keyword loads the file AFTER the HTML is parsed.

Additional script loading strats: https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/The_head_metadata_in_HTML#applying_css_and_javascript_to_html

## Event Listeners

Events are actions that occur on your webpage, e.g. mouse clicks or key presses. JS allows the page to listen and react to these.

- Attach functions' attributes directly to HTML elements
- Using `on_event_` property on the DOM object
- Attach event listeners to the nodes

Event listeners are the preferred method, but the others are in use as well.

### Attach functions' attributes directly to HTML elements

`<button onclick="alert('Hello world')">Click me</button>`

This is less than ideal because the HTML is being cluttered with JavaScript. You can also only have one "onclick" event per element.

### Using `on_event_` property on the DOM object

HTML: `<button id="btn">Click me</button>`

JavaScript:

    const btn = document.querySelector('#btn');
    btn.onclick = () => alert("Hello World");

### Attach event listeners to the nodes

HTML: `<button id="btn">Click Me Too</button>`

JavaScript:

    const btn = document.querySelector('#btn');
    btn.addEventListener('click', () => {
        alert("Hello World");
    });

You can use named functions in all these methods and it's probably a good idea.

The last method is the most flexible one.

    btn.addEventListener('click', function(e)) {
        console.log(e);
    });

NOTE: In this, the `function (e)` is a callback from addEventListener. `e` is an object that references the event itself. With this object, you can access many useful properties/functions e.g. exactly which input was received, or information about the event's target - the DOM node that was clicked.

    btn.addEventListener('click', function(e) {
        console.log(e.target);
        e.target.style.background = 'blue';
    });

### Attaching listeners to groups of nodes

If you want to attach lots of similar event listeners to many elements, get a nodelist of all items and iterate through them.

    <div id="container">
        <button id="1">Click Me</button>
        <button id="2">Click Me</button>
        <button id="3">Click Me</button>
    </div>

JS:
    const buttons = document.querySelectorAll('button');

    buttons.forEach((button) => {
        button.addEventListener('click', () => {
            alert(button.id);
        });
    });

Some more useful events:
- click
- dblclick
- keydown
- keyup

## Forms

**NOTE:** You can't nest a form within a form. So don't.

The `<form>` element is a container, just like a div. It wraps all the inputs a user will interact with on a form.

    <form action="example.com/path" method="post">

    </form>

Accepts 2 essential attributes: 
- `action`, takes a URL which tells the form where to send its data to be processed. (the backend stuff)
- `method`, which HTTP request method it should use to submit the form. `GET` and `POST` are the most common ones.

`GET`: when you want to retrieve something from the server, e.g. getting Google search results.

`POST`: when you want to change something on the server, e.g. a user makes an account or makes a payment.

### Form Controls

These are the elements users will interact with, e.g. text boxes, checkboxes, dropdowns, buttons. Most common form controls:

#### Input

Most versatile: accepts a `type` attribute so the browser knows what to expect and how to render the element. e.g. `<input type="text">`

#### Labels

Give your inputs a label to inform the user what they are supposed to enter.

    <form action="eg.com/path" method="post">
        <label for="first_name">First Name:</label>
        <input type="text" id="first_name">
    </form>

The `for` attribute associates the label with an input, taking an existing `id` attribute. This makes the input *focused* when a user clicks on the label, which is important for accessibility/UX.

#### Placeholder

Adding a `placeholder` attribute to an input, like

    <label for="first_name">First Name:</label>
    <input type="text" id="first_name" placeholder="First name here">

will have the attribute value appear in the input field as an example.

#### Name

The `name` attribute added to an input informs the **backend** of what each piece of data represents.

`<input type="text" id="first_name" name="first_name">` The `name` acts as a variable for the input. **Form input should always have a `name` attribute!** Otherwise it will be ignored when the form is submitted.

    "form": {
        "age": "5", 
        "first_name": "Natan", 
        "last_name": "Rad"
    }, 

Example of submitted form data.

### Form Controls outside of forms

You can use any form control elements outside of `<form>`, even when you don't have a backend server.

### The Type Attribute

`email` inputs are special text inputs just for emails. On mobile, they will display a different keyboard with an @ for easier typing. They also validate that an email address is correctly formatted.

`<input type="email" id="user_email" name="email">`

`password` inputs are another specialized type. The text inputted is automatically masked with `*`.

`number` inputs only accept number values.

`date` inputs provide a better user experience for choosing dates by rendering a simple calendar.

### Text Area

It's not technically an input element, BUT the `<textarea></textarea>` element provides an input box that can accept text that spans multiple lines, e.g. user comments and reviews. It can also be resized by dragging the bottom right corner. You can control this with the `resize` attribute.

Unlike input elements, they *do* have a closing tag, so you can wrap some initial content for it to display.

`<textarea rows="20" cols="60">Text here</textarea>` The `rows` and `cols` attributes allow you to control the initial height (rows) and width (cols) of this element.

### Selection Elements

When users need to select a value from an existing list.

### Select Dropdown

Syntactically, select elements have similar markup to unordered lists. It wraps option elements which are the options to be selected.

    <select name="Car">
        <option value="mercedes">Mercedes</option>
        <option value="tesla">Tesla</option>
        <option value="volvo" selected>Volvo</option>
        <option value="bmw">BMW</option>
        <option value="mini">Mini</option>
        <option value="ford">Ford</option>
    </select>

All option elements need a `value` attribute, sent to the server when the form is submitted.

You can set a default selected option with the `selected` attribute.

With the `<optgroup label="group-name"></optgroup>` element, you can split options into groups, with a `label` for the group.

By adding `multiple size="2"`, 2 options can then be selected at once.

### Radio Buttons

If you have 5 or less options to choose from, you can use radio buttons (the circular ones you click on). 

    <div>
        <input type="radio" id="child" name="ticket_type" value="child">
        <label for="child">Child</label>
    </div>

    <div>
        <input type="radio" id="adult" name="ticket_type" value="adult">
        <label for="adult">Adult</label>
    </div>

In this example, selecting one of the radio buttons will deselect any others, because they have the same `name` attribute, so the browser knows they are part of the same group of options.

As with dropdowns, adding `checked` to the end of a radio input will make that selected by default.

### Checkboxes

Similar to radio buttons, but allow multiple selections.

    <h1>Pizza Toppings</h1>

    <div>
        <input type="checkbox" id="sausage" name="topping" value="sausage">
        <label for="sausage">Sausage</label>
    </div>

    <div>
        <input type="checkbox" id="onions" name="topping" value="onions">
        <label for="onions">Onions</label>
    </div>

You can also use a single checkbox if you want something to be set to true or false, e.g. agreeing to a newsletter.

Checkboxes can also be set to checked by default with `checked` attribute.

### Buttons

Clickable buttons to submit forms and trigger other actions.

`<button>Click Me</button>`

Also accepts a `type` attribute that tells the browser what type of button we have.

### Submit

`<button type="submit">Submit</button>` will submit the form it is contained within. Buttons are submit buttons are default.

#### Reset

`<button type="reset">Reset</button>` Clears all data that has been entered and resets all inputs.

#### Generic Button

`<button type="button">Click to Toggle</button>` Simply a general use button, commonly used with JS for creating interactive UIs.

**NOTE:** A button within a form with `type="submit"` will always try to make a new request and submit data back to the server. Hence, always specify button types for buttons that *aren't* for submitting the data.

### Organising Form Elements

For a user friendly experience:
- Use the correct inputs for the data you want
- organise forms into sections with `<fieldset></fieldset>`, a container element for grouping form inputs into logical units
- Give fieldsets a heading or caption with the `<legend>Title Name Here</legend>` element. Comes *after* an opening `<fieldset>`.

Common use case: grouping radio buttons together and giving them a title.

### Autocomplete box

    <label for="myFruit">What's your favourite fruit?</label>
    <input type="text" name="myFruit" id="myFruit" list="suggestion" />

    <datalist id="suggestion">
        <option>Apple</option>
        <option>Banana</option>
        <option>Pear</option>
    </datalist>

The `id` value of the datalist is called in the `list` attribute of the associated input. When the user is typing their answer, the datalist options are used to auto-complete, typically as a dropdown box listing possible matches.

Almost all browsers support datalists, BUT if supporting old browsers like IE below 10, there is a fallback trick:

    <datalist id="fruitList">
        <label for="suggestion">or pick a fruit</label>
        <select id="suggestion" name="altFruit">
            <option>Apple</option>
            <option>Banana</option>
            <option>Blackberry</option>
            <option>Blueberry</option>
            <option>Lemon</option>
            <option>Lychee</option>
            <option>Peach</option>
            <option>Pear</option>
        </select>
    </datalist>

#### Datalist fallback trick

Nesting an alternative, a select box, within the datalist, and having both elements draw on the same options. If you use this, remember to collect data for both the input and the select.

#### Less obvious datalist uses

The `list` attribute and `datalist` element can be ysed with any kind of widget requiring a user input. e.g. for browsers that support `datalist` on `range` input types, a small tick mark will be displayed above the range for each `datalist` option.

And for browsers that support `datalist` and `input type="color"`, you can display a customised palette of colours as the default, while still making the full palette available.

### Meters and progress bars

Visual representations of numeric values.

Progress bar: `<progress max="100" value="75">75/100</progress>

Represents a value that changes over time up to a `max` value. For implementing anything requiring progress reporting, e.g % of total files downloaded, number of questions filled in on a quiz.

The content inside `<progress></progress>` (likewise for meters) is a fallback for no support, and for screen readers.

Meter: `<meter min="0" max="100" value="75" low="33" high="66" optimum="50">75</meter>`

A fixed value in a range specified by `min` and `max`, which renders as a coloured progress bar based on where the value sits in the ranges specified by the additional attributes.

For `optimum`:
- defines which part of the range is "preferred".
- if the optimum value is in the lower part of the range, then lower is preferred, medium is average, high is worst. Vice versa for higher part of range.
- if in medium part of range, both low and high ranges are considered average.

All browsers that accept `<meter>` use those values to change the colour of the meter bar according to the current `value`:
- preferred is green
- average is yellow
- worst is red

IE doesn't support progress and meter bars, but all other browsers do.

## Structuring Forms

Forms are more complex than other HTMl elements and require mixing CSS and JS to properly use them.

Design your form in advance via a quick mockup. The bigger your form, the higher the risk of frustrating users, so only ask for what you need.

It's common to wrap a label and its widget with `<li>` within a `<ul>` or `<ol>`. `<p>` and `<div>` are also commonly used. For structuring multiple checkboxes or radio buttons, use lists.

In addition to `<fieldset>`, it's common to use `<h1>`, `<h2>` etc. and `<section>` to structure complex forms.

### Accessibility

Don't apply multiple labels to one widget as some assistive technologies have issues with them. If you need to, you should nest a widget and its labels inside a single `<label>`.

    <p>Required fields are followed by <span aria-label="required">*</span>.</p>
    <div>
        <label for="username">Name: <span aria-label="required">*</span></label>
        <input id="username" type="text" name="username">
    </div>

The p at the top states a rule for required elements. It must be included *before* it is used so that users can learn what it means before they encounter it. The `title` in that rule, `required`, will be read aloud due to the `aria-label` attribute.

So, the form element in this example would be read out as "name required edit text" (or close ish).

## Styling Forms

### Challenges with Styling Forms

- Each browser has its own default way of styling forms. To achieve consistency among all browsers, you'll need to override these default styles yourself.
- Some form control elements, like radio buttons and checkboxes, are a bit trickier to style, but there are guides and new CSS properties to help.
- Others, e.g. calendars, date pickers, basically can't be styled, and if you need to control the appearance, you'll have to build custom form controls with JS or use a JS library.

### Fonts and Text

You can use `@font-face` with widgets. However, browser behaviour is inconsistent, and some widgets don't inherit `font-family` and `font-size` from their parents.

To make form appearance consistent with the rest of the content:

    button,
    input,
    select,
    textarea {
        font-family: inherit;
        font-size: 100%;
    }

### Box sizing

Each widget has its own rules for border, padding and margin. To make this consistent, use the `box-sizing` property along with some consistent values for other properties:

    input,
    textarea,
    select,
    button {
        width: 150px;
        padding: 0;
        margin: 0;
        box-sizing: border-box;
    }

This ensures all elements occupy the same amount of space.

### Legend placement

By default `<legend></legend>` (form section sub-heading/caption) is always positioned over the top border of its `<fieldset></fieldset>` parent (top left corner). 

To position it in the bottom right (as an example),

    fieldset {
        position: relative;
    }

    legend {
        position: absolute;
        bottom: 0;
        right: 0;
    }

Fieldset needs to be positioned too, so that legend can be positioned relative to it.

Legend is very important for accessibility. However, positioning it in this way changes the appearance, not the structure of the page, so any assistive technology reading won't be affected.

### Focus styling

It's important to have focus styling on inputs e.g. input, textarea, for accessibility.

    input:focus,
    textarea:focus {
        background: rgba(0, 0, 0, 0.1);
        border-radius: 5px;
    }

### Textarea

Textarea defaults to inline-block rendering. Most important properties here are `resize` and `overflow`. You could stop the user from resizing with `resize: none;` but it's best not to prevent it unless it's necessary. 

Some browsers default to `overflow: auto;`, and others `scroll`, so it's better to specify which one you want.

    textarea {
        display: block;

        padding: 10px;
        margin: 10px 0 0 -10px;
        width: 100%;
        height: 90%;

        border-right: 1px solid;

        /* resize  : none; */
        overflow: auto;
    }

## Pseudo-classes for Forms

CSS 2.1 (original pseudo classes)

- `:hover` - only when hovered by a mouse
- `:focus` - only when focused, i.e. tabbed to via the keyboard
- `:active` - selects an element only while it's being activated (i.e. while being clicked on, or wehen the ENTER key is being pressed for keyboard activation)

CSS 3 added more pseudo classes. The main ones are:

- `:required` and `:optional` - targets required/optional form controls
- `:valid`/`:invalid`, and `:in-range`/`:out-of-range` - target form controls that are valid/invalid according to form validation constraints, or in range/out of range
- `:enabled`/`:disabled`, and `:read-only`/`:read-write` - targets enabled/disabled form controls (e.g. with the `disabled` HTML attribute) and read-write/read-only form controls (e.g. with the `readonly` HTML attribute set)
- `:checked`/`:indeterminate`/`:default` - respectively, targets checkboxes/radio buttons that are: checked/indeterminate state (neither checked or not checked), and in the default selected option (e.g. an `<input type="checkbox" checked>` will be selected by `default`, or an `<option selected>`)

Some of the new pseudo-classes don't have great browser support yet, but the above ones are commonly used and supported. 

A lot of these pseudo-classes style form controls based on their **validation state** (is their data valid or not).

## Client-side form validation

## HTML Render Order

Elements that are declared first (from top to bottom) are considered "behind" elements that are below them or nested on them.

## Linking to a Header

Instead of using an `<a href=""></a>` anchor, use `#id`. I'm not sure how this would work for external links, but it definitely works for internal links.

    <!-- <a> element links to the section below -->
    <p><a href="#Section_further_down"> Jump to the heading below </a></p>

    <!-- Heading to link to -->
    <h2 id="Section_further_down">Section further down</h2>

## Template tags

Anything in these tags will be hidden when the page loads. Use JS to display it.

    <template>
    </template>

Important note: please remember to clone the template's CONTENT before instantiating it! (cloning the template itself will not work, you need `template.content`).

Also, *you need to make a new clone each time you want to use the template*. Otherwise you'll just overwrite all existing elements that use that template.

    function showContent() {
        let temp = document.querySelector('template');

        let clone = temp.content.cloneNode(true);

        document.body.appendChild(clone);
    }

> `cloneNode(isDeep)` if `true` will deep clone the node (clone all subtrees). `false` will only clone the node itself. Any subtrees, including any text the node contains, is not cloned. P.S. `isDeep` won't affect void elementrs, e.g. img or input.