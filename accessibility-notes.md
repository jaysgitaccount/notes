# Accessibility Notes
I started this file after trying to add keyboard controls to a custom combobox in my Timezone Tracker project. I didn't want to build a widget from scratch, but I had to after I realised that Firefox Android completely doesn't support datalists at the moment even though caniuse says they do (due to a bug that hasn't been fixed for 4 years). The length of the dropdown I needed necessitated a combobox, so here we are.

## Combobox with list autocomplete
I'm using [this](https://www.w3.org/WAI/ARIA/apg/example-index/combobox/combobox-autocomplete-list) as a guideline for functionality.

**NOTE**: Apparently "visual focus" and "DOM focus" are different.

### Visual features
- Clicking a text input's label should focus the input.
- `hover` causes the same styling as `focus`
- `cursor: pointer` when hovering on the combobox and list
- Selected list item creates a 2px border above and below the option.
- When a selected item is deselected, change border to 0px and add increase padding by 2px.
- Do not use transparency to create colour contrast between elements (it is handled differently across browsers)

#### `aria-activedescendant`
Browsers do not manage visibility of `aria-activedescendant` (used by the browser's zoom feature) like they do for `focus` elements, so this needs to be managed manually.

### Keyboard Support
Some of the these are already part of my design so I won't use those controls. e.g manually opening the menu is not required, but manually closing it probably is.

- Down arrow: while keeping DOM focus on the text input, move visual focus to first suggested value.
- Up arrow: move visual focus to the LAST suggested value (keep focus on text input)
- Enter: close menu
- Esc: if menu is displayed, close it. If listbox is not displayed, clear textbox.

#### Listbox popup
While focusing in here, keep DOM focus on textbox. The value of `aria-activedescendant` is set to a value that refers to the listbox option that is visually focused.

- Right arrow: visually focus textbox and move editing cursor 1 char to the right
- Left arrow: visually focus textbox and move editing cursor 1 char to the left
- Home/End: usual
- Typing any "printable character" should move visual focus to textbox and type that character in, filtering list

### ARIA labels
It seems like web accessibility is a combination of proper HTML tags/attributes and keyboard functionality (put very basically). Elements that serve a purpose need to have a set `role`, and any others (e.g. wrappers) need `role="none"`.

Additional `aria-` attributes can be set to serve as extra indicators about what this element does. Some of these should be dynamically set, e.g. `aria-expanded` should be `false` if the listbox is closed and `true` if it's open.

### Tabindex
I believe the notes say that you should NOT be able to tab within a combobox list, you should be navigating using up/down/left/right, and tabbing will take you to the next tabbable element. Hence `tabIndex=-1`