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
  // replace images and set alignments by clicking the image in the editor
  image: {
    uploadURL: "/api/myEndpoint",
    dropzoneOptions: {}
  },

  // add images on file from front-end
  // splits upload dashboard in half and displays the dropzone on the right with the on file images on the left.
  // needs css work done
  imagesOnFile: [
    'http://placehold.it/600x200/',
    'http://placehold.it/300x200/',
  ]

  // disable direct image paste
  imagePaste: false

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

  // insert short html clips
  addShortHtml: [
    {
      name: 'coolness',
      html: '<strong>cool html</strong>'
    }
  ]

  // insert Templates
  templates: [
    {
      name: 'template name',
      html: 'template html',
    }
  ]
}
```
Available Modules:
 - bold
 - italic
 - underline
 - strikethrough
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
 - addShortHtml
 - templates
 - imagesOnFile (passive)
 - imageUpdate (passive)

Note on the image upload API endpoint:
- Image is uploaded by `multipart/form-data`
- Your endpoint must respond back with a string, the URL for the image - e.g. 
```js
 return `http://placehold.it/600x200/`
```


Edits to the [original](https://github.com/chmln/vue-wysiwyg) inspired by: [itsyub](https://github.com/itsyub/vue-wysiwyg-lite)
