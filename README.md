# `<file-input>` Element
A custom `HTMLInputElement` that accepts a file and provides a simple preview for common file value use-cases.

Package size: ~7kb minified, ~9kb verbose.

## Quick Reference
```html
<form>
    <file-input name="custom-file" accept="text/plain, text/json, application/msword">
        <span slot="placeholder">
            <span class="icon"><img src="custom-file-icon.png" /></span>
            <span class="title">Click or Drag and Drop a file here</span>
        </span>
    </file-input>
</form>
<script type="module" src="/path/to/file-input[.min].js"></script>
```
*Note: For selecting images with a preview, consider the [`<image-input>` element](https://github.com/catapart/magnitce-image-input).*

## Demos
https://catapart.github.io/magnitce-file-input/demo/

## Support
- Firefox
- Chrome
- Edge
- <s>Safari</s> (Has not been tested; should be supported, based on custom element support)

## Getting Started
 1. [Install/Reference the library](#referenceinstall)

### Reference/Install
#### HTML Import (not required for vanilla js/ts; alternative to import statement)
```html
<script type="module" src="/path/to/file-input[.min].js"></script>
```
#### npm
```cmd
npm install @magnit-ce/file-input
```

### Import
#### Vanilla js/ts
```js
import "/path/to/file-input[.min].js"; // if you didn't reference from a <script>, reference with an import like this

import { FileInputElement } from "/path/to/file-input[.min].js";
```
#### npm
```js
import "@magnit-ce/file-input"; // if you didn't reference from a <script>, reference with an import like this

import { FileInputElement } from "@magnit-ce/file-input";
```

---
---
---

## Overview
The `<file-input>` element was designed to package the collection, inspection, and clearing of files in a form simpler and with a default style/layout that more closely aligns with other inputs.

The element provides a "Clear Selection" link, which clears the selected file from the input, as well as a "View Document" link which will attempt to open the selected file in a new browser context (Tab/Window). The type of file will determine how a browser handles that function by default. Text files, for example, will display their text content, while binary files will typically trigger the file-download process.

## Attributes
|Attribute Name|Description|
|-|-|
|`placeholder`|Sets the text content of the placeholder.|
|`name`|When used in a `<form>`, assigns a property name to the `FormData` object that is generated from that `<form>` element.|
|`accept`|Defines the types of files that are able to be selected. Default Value: `[empty]`|
|`required`|When used in a `<form>`, prevents the `<form>` from submitting unless a file has been selected by this input. Also provides the `:invalid` state for styling, when no file has been selected.|

## Events
The `<file-input>` element dispatches a `change` event when a file has been selected, or a selection is cleared. Like other inputs, the event can be handled and the value can be collected by accessing the `<file-input>` element's `value` property. Like other `file` type inputs, the selected file can also be collected by accessing the `files` property, with an index of the target selection.

```html
<file-input></file-input>
<script>
    const input = document.querySelector('file-input');
    input.addEventListener('change', (event) =>
    {
        console.log(event.target.value);
        console.log(event.target.files);
    });
</script>
```

In addition to the `change` event, the `<file-input>` element also dispatches a `deny` event if a user attempts to drop a file on the input when that input's `accept` attribute has disallowed that file type.  
The `deny` event is a `CustomEvent` with a `detail` property that provides the file the user dropped, the error message, and the list of acceptable file types for that input.

In this example, the `deny` event is being handled by logging each of the detail properties:
```html
<file-input></file-input>
<script>
    const input = document.querySelector('file-input');
    input.addEventListener('deny', (event) =>
    {
        console.error(event.detail.message);
        console.info(event.detail.accepted);
        console.log(event.detail.file.name, event.detail.file.size);
    });
</script>
```

## Parts
|Part Name|Description|Element
|-|-|-|
|`label`|A `<label>` element that acts as a container and provides click functionality for invoking the nested input.|`HTMLLabelElement`|
|`input`|The input that invokes the browser functionality like launching a file browser. Hidden by default.|`HTMLInputElement[type="file"]`|
|`field`|The container for the placeholder, the preview, and the labels. This part includes everything that is not the "Clear" and "View Document" links.|`HTMLSpanElement`|
|`icon`|An icon that represents a file.|`HTMLSpanElement`|
|`status`|A container for either the placeholder text, or the filename of the selected file.|`HTMLSpanElement`|
|`filename`|Displays the filename of the selected file. Hidden when there is no selection.|`HTMLSpanElement`|
|`placeholder`|Displays the placeholder text. Hidden when a selection has been made.|`HTMLSpanElement`|
|`clear`|A link that clears the input, if a selection has been made.|`HTMLAnchorElement`|
|`view-link`|A link that opens the selected file in a new browser context (tab/window).|`HTMLAnchorElement`|

## Slots
The `<file-input>` element exposes the following `slot`s: 
|Slot Name|Description|Default
|-|-|-|
|`placeholder`|Text content displayed whenever a selection has not been made.|`HTMLSpanElement`|
|`icon`|An icon that represents a file.|`HTMLSpanElement`|
|`clear`|A link that clears the input, if a selection has been made.|`HTMLAnchorElement`|
|`view-link`|A link that opens the selected file in a new browser context (tab/window).|`HTMLAnchorElement`|

## Styling
The `<file-input>` element can be styled with CSS, normally with limited effect. The `<file-input>` element also utilizes the shadowDOM for layout, so styling the internal layout elements must be done using a `::part()` selector.

Unlike other inputs, the `<file-input>` element includes elements that are not normally considered part of the "input" area. The "Clear" and "View Fullsize" links are not contained within the area that is traditionally understood as the "field".  
Because of this, most of the styling that would be done to an `<input>` element should be done to the `field` part, instead. For example, setting the `border` or `background-color` should be done on the `field` part, rather than on the `<file-input>` element.

In this example, the `field` part is being selected for styling:
```css
file-input::part(field)
{
    /* styling */
}
```
For a list of all part names, see the [parts](#parts) section.

Due to the nature of the `::part()` selector, and for general convenience, the `<file-input>` element also implements a `--border-color` variable that can be set on the element, which will act on the field like any other input.  
For example, both of these inputs are targeted to represent an `:invalid` state:
```css
input[type="text"]:invalid
{
    border-color: red;
}
file-input:invalid
{
    /* unlike other inputs, the file-input includes links that
    *  are outside of its field. to simplify setting the field
    *  border color, --border-color has been provided
    */
    --border-color: red;
}
```

As an additional convenience, the `<file-input>` element provides a block-style layout option, rather than the default inline-style layout. Assigning the `block` class will, like in this example, will render the input in the block-style layout:
```html
<file-input class="block"></file-input>
```

## License
This library is in the public domain. You do not need permission, nor do you need to provide attribution, in order to use, modify, reproduce, publish, or sell it or any works using it or derived from it.