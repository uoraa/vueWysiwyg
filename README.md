## Usage

Works in all modern browsers 

### Install vue-wysiwyg

In your `main.js`:

```js
import wysiwyg from "vueWysiwyg";
Vue.use(wysiwyg, {}); // config is optional. more below
```

Also make sure to load the stylesheet.
The exact syntax will depend on what preprocessor you use.

```css
@import "vueWysiwyg/dist/vueWysiwyg.css";
```

In your components:
```html
<wysiwyg v-model="myHTML" />
```

## Config options

All keys are optional.

```js
{
  // { [module]: boolean (set true to hide) }
  hideModules: { "bold": true },

  // you can override icons too, if desired
  // just keep in mind that you may need custom styles in your application to get everything to align
  iconOverrides: { "bold": "<i class='your-custom-icon'></i>" },

  // if the image option is not set, images are inserted as base64
  image: {
    uploadURL: "/api/myEndpoint",
    dropzoneOptions: {}
  },

  // limit content height if you wish. If not set, editor size will grow with content.
  maxHeight: "500px",

  // set to 'true' this will insert plain text without styling when you paste something into the editor.
  forcePlainTextOnPaste: true,

  // set default paragraph seperator
  paragraphSeperator: 'div',

  //set toolbar position to bottom
  toolbarPosition: 'bottom',

  // insert preset links
  addedlinks: [
    {
      name: 'link name',
      link: 'http://www.linktosomewhere.com'
    }
  ],

}
```
Available Modules:
 - bold
 - italic
 - underline
 - justifyLeft
 - justifyCenter
 - justifyRight
 - headings
 - link
 - code
 - orderedList
 - unorderedList
 - image
 - table
 - removeFormat
 - separator
 - addedlinks

Note on the image upload API endpoint:
- Image is uploaded by `multipart/form-data`
- Your endpoint must respond back with a string, the URL for the image - e.g. `https://static.pexels.com/photos/177809/pexels-photo-177809.jpeg`


Edits to the [original](https://github.com/chmln/vue-wysiwyg) inspired by: [itsyub](https://github.com/itsyub/vue-wysiwyg-lite)

