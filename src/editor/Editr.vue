<template lang="pug">
.editr
    imageUpdate(
      v-if="modalVis"
      :data="modalData"
      :options="mergedOptions",
      @returnImage="newImageData"
    )
    .editr--toolbar(:class="mergedOptions.toolbarPosition")
        Btn(
            v-for="(module,i) in modules",
            :module="module",
            :options="mergedOptions",
            :key="module.title + i",

            :ref="'btn-'+module.title",
            :title="module.description || ''"
        )

    .editr--content(ref="content", contenteditable="true", tabindex="1", :placeholder="placeholder")
</template>
<script>
import bus from 'src/editor/bus.js';
import debounce from "debounce";
import Btn from "./Button.vue";
import imageUpdate from "./imageUpdate.vue";

import bold from "./modules/bold.js";
import italic from "./modules/italic.js";
import underline from "./modules/underline.js";

import alignLeft from "./modules/alignLeft.js"
import alignCenter from "./modules/alignCenter.js"
import alignRight from "./modules/alignRight.js"


import headings from "./modules/headings.vue";
import hyperlink from "./modules/hyperlink.vue";
import code from "./modules/code.js";
import list_ordered from "./modules/list_ordered.js";
import list_unordered from "./modules/list_unordered.js";

import image from "./modules/image.vue";
import table from "./modules/table.vue";

import removeFormat from "./modules/removeFormat.js";

import separator from "./modules/separator.js";
import addedlinks from "./modules/addedlinks";
import addShortHtml from "./modules/shorthtml";
import templateHtml from "./modules/template";
import strikeThrough from "./modules/strikeThrough.js";

const modules = [
  headings, separator, bold, italic, underline, strikeThrough, separator,
  alignLeft, alignCenter, alignRight, separator, hyperlink, code,
  list_ordered, list_unordered, separator,
  image, separator, table, separator,
  removeFormat, separator, addedlinks, addShortHtml, templateHtml
];
// console.log(modules);

export default {
  model: {
    prop: "html",
    event: "html"
  },

  props: {
    html: {
      type: String,
      default: ""
    },
    placeholder: {
      type: String,
      default: "Enter text..."
    },
    options: Object,
  },


  components: { Btn, imageUpdate },

  data() {
    return {
      selection: "",
      selectedNode: null,
      modalVis: false,
      modalData: {},
    }
  },

  computed: {
    mergedOptions() {
      return { ...bus.options, ...this.options }
      // console.log(this.modules);
    },

    modules() {
      const customIcons = this.mergedOptions.iconOverrides;
      if (!this.mergedOptions.templates) {
        this.mergedOptions.hideModules.template = true;
      } else {
        if (this.mergedOptions.templates.length === 0) {
          this.mergedOptions.hideModules.template = true;
        }
      }

      if (!this.mergedOptions.addShortHtml) {
        this.mergedOptions.hideModules.shorthtml = true;
      } else {
        if (this.mergedOptions.addShortHtml.length === 0) {
          this.mergedOptions.hideModules.shorthtml = true;
        }
      }

      if (this.mergedOptions.addedlinks === undefined) {
        this.mergedOptions.hideModules.addedlinks = true;
      } else {
        if (this.mergedOptions.addedlinks.length === 0) {
          this.mergedOptions.hideModules.addedlinks = true;
        }
      }
      return modules
        .filter(
          m => this.mergedOptions.hideModules === undefined ||
          !this.mergedOptions.hideModules[m.title]
        )
        .map(mod => {
          if (customIcons !== undefined && customIcons[mod.title] !== undefined) {
            mod.icon = customIcons[mod.title];
          }
          return mod;
        })
        .concat(this.mergedOptions.customModules);
    },

    btnsWithDashboards() {
      if (this.modules)
        return this.modules.filter(m => m.render);
      return [];
    },

    innerHTML: {
      get() {
        return this.$refs.content.innerHTML;
      },

      set(html) {
        if (this.$refs.content.innerHTML !== html) {
          this.$refs.content.innerHTML = html;
        }
      }
    }
  },

  methods: {
    saveSelection() {
      if (window.getSelection !== undefined) {
        this.selection = window.getSelection();
        if (this.selection.getRangeAt && this.selection.rangeCount) {
          return this.selection.getRangeAt(0);
        }
      } else if (document.selection && document.selection.createRange) {
        return document.selection.createRange();
      }
      return null;
    },

    restoreSelection(range) {
      if (range) {
        if (window.getSelection !== undefined) {
          this.selection = window.getSelection();
          this.selection.removeAllRanges();
          this.selection.addRange(range);
        } else if (document.selection && range.select)
          range.select();
      }
    },
    clearSelection() {
      this.selection = null;
      const selection = window.getSelection();

      if (selection) {
        if (selection.empty !== undefined) {
          selection.empty();
        }
        if (selection.removeAllRanges !== undefined) {
          selection.removeAllRanges();
        }
      }
    },
    exec(cmd, arg, sel) {
      sel !== false && this.selection && this.restoreSelection(this.selection);
      document.execCommand(cmd, false, arg || "");
      this.clearSelection();

      this.$nextTick(this.emit);
    },

    onDocumentClick(e) {
      for (let i = 0; i < this.btnsWithDashboards.length; i++) {
        const btn = this.$refs[`btn-${this.btnsWithDashboards[i].title}`][0];
        if (btn && btn.showDashboard && !btn.$el.contains(e.target))
          btn.closeDashboard();
      }
    },

    emit() {
      this.$emit("html", this.$refs.content.innerHTML);
      this.$emit("change", this.$refs.content.innerHTML);
    },

    onInput: debounce(function() {
      this.emit();
    }, 300),

    onKeyDown: debounce(function() {
      if (navigator.userAgent.indexOf('MSIE') > 0 || navigator.userAgent.match(/Trident.*rv:11\./)) {
        setTimeout(() => {
          this.emit();
        }, this)
      }
    }, 300),

    onFocus() {
      document.execCommand("defaultParagraphSeparator", false, this.mergedOptions.paragraphSeparator)

    },

    onContentBlur() {
      // save focus to restore it later
      this.selection = this.saveSelection();
    },
    onImagePaste(e) {

        var image = false;
        for (let i = 0; i < e.clipboardData.items.length; ++i) {
          let item = e.clipboardData.items[i];
          // console.log("kind:", item.kind, "type:", item.type);
          let type = item.type;
          if (item.kind == 'file' && type.includes('image')) {
            image = true;
          }
        }
        if (image == true) {
          e.preventDefault();
        }

    },
    onPaste(e) {
      e.preventDefault();

      // get a plain representation of the clipboard
      var text = e.clipboardData.getData("text/plain");

      var items = (event.clipboardData || event.originalEvent.clipboardData).items;
      // console.log(JSON.stringify(items));

      // insert that plain text text manually
      document.execCommand("insertHTML", false, text);
    },

    syncHTML() {
      if (this.html !== this.$refs.content.innerHTML)
        this.innerHTML = this.html;
    },
    newImageData(value) {
      this.modalVis = false;
      var target = value.original;
      target.src = value.newUrl;
      target.removeAttribute('align');
      if (value.alignment) {
        target.setAttribute('align', value.alignment);
      }
      target.removeAttribute('style');
      var maxWidth = 'max-width: ' + value.maxWidth + 'px;';
      target.setAttribute('style', maxWidth);
      this.emit();
    },
    clicked(e) {
      e = e || window.event;
      var target = e.target || e.srcElement;
      var alignment = '';
      var maxWidth;
      if (target.hasAttribute("align")) {
        alignment = target.getAttribute("align");
      }
      if (target.hasAttribute("style")) {
        maxWidth = parseInt(target.style.maxWidth, 10);
      } else {
        maxWidth = target.naturalWidth;
      }
      var tag = target.tagName.toString();
      if (tag == 'IMG') {
        this.modalVis = true;
        this.modalData = {
          target: target,
          src: target.src,
          alignment: alignment,
          maxWidth: maxWidth,
        };
        // var imgUrl = prompt("New Image Url", "");
        // if (imgUrl != null) {
        //   target.src = imgUrl;
        // }
        // this.emit();
      }
    }
  },

  mounted() {
    this.unwatch = this.$watch("html", this.syncHTML, { immediate: true });

    document.addEventListener("click", this.onDocumentClick);

    this.$refs.content.addEventListener("click", this.clicked);
    this.$refs.content.addEventListener("focus", this.onFocus);
    this.$refs.content.addEventListener("input", this.onInput);
    this.$refs.content.addEventListener("keydown", this.onKeyDown);
    this.$refs.content.addEventListener("cut", this.onKeyDown);
    this.$refs.content.addEventListener("paste", this.onKeyDown);
    this.$refs.content.addEventListener("blur", this.onContentBlur, { capture: true });

    if (this.mergedOptions.forcePlainTextOnPaste === true) {
      this.$refs.content.addEventListener("paste", this.onPaste);
    }

    if (this.mergedOptions.imagePaste === false) {
      this.$refs.content.addEventListener("paste", this.onImagePaste);
    }

    this.$refs.content.style.maxHeight = this.mergedOptions.maxHeight;
  },

  beforeDestroy() {
    this.unwatch();
    document.removeEventListener("click", this.onDocumentClick);

    this.$refs.content.removeEventListener("blur", this.onContentBlur);
    this.$refs.content.removeEventListener("input", this.onInput);
    this.$refs.content.removeEventListener("keydown", this.onKeyDown);
    this.$refs.content.removeEventListener("cut", this.onKeyDown);
    this.$refs.content.removeEventListener("paste", this.onKeyDown);
    this.$refs.content.removeEventListener("focus", this.onFocus);
    this.$refs.content.removeEventListener("click", this.clicked);
  }
}

</script>

<style lang="stylus">
$offwhite = #f6f6f6
$buttonWidth = 32px
$buttonHeight = 32px
$svgSize = 16px

.editr
    border 1px solid darken($offwhite, 7.5%)
    width 100%
    position relative

    .editr--toolbar
        &.bottom
            ~.editr--content
                padding-bottom 50px

.editr--toolbar
    background $offwhite
    border-bottom 1px solid darken($offwhite, 7.5%)
    position relative
    display flex
    height $buttonHeight

    &.bottom
        position absolute
        width 100%
        bottom 0
        border-bottom 0
        border-top 1px solid #e4e4e4
    a
        display inline-block
        width $buttonWidth
        max-width $buttonHeight
        height $buttonHeight
        color #333
        fill #333
        cursor pointer
        text-align center
        line-height 1

        &:hover
            background alpha(black, 0.1)

        &:active
            background alpha(black, 0.2)

        svg
            width $svgSize
            height $svgSize
            margin (($buttonHeight - $svgSize)/2) auto

            path
                fill inherit

        &.vw-btn-separator
            width 1px
            margin 0 8px

            &:hover
                background initial
                cursor default

            i.vw-separator
                border-left 1px solid alpha(black, 0.1)
                height 100%
                position absolute
                width 1px

    .dashboard
        width 100%
        position absolute
        top 32px
        left 0
        text-align left
        padding 5px
        background alpha(white, 0.95)
        border 1px solid $offwhite
        box-sizing border-box
        z-index 16

    &.bottom

        .dashboard
            top unset
            bottom 100%


.editr--content
    min-height 150px
    padding 12px 8px 16px 8px
    line-height 1.33
    font-family inherit
    color inherit
    overflow-y auto

    &[contenteditable=true]:empty:before
        content: attr(placeholder);
        color alpha(black, 0.3)
        display: block; /* For Firefox */

    img
        max-width 100%

    img:hover
      border: 2px dotted black
      cursor: context-menu

    table
        /* width 100% */
        border-collapse collapse

        th
            text-align left

        th, td
            border 1px solid #dddddd
            padding 2px


    &:focus
        outline 0

    ul, ol
        li
            list-style-position: inside;

@media screen and (max-width 320px)
    .editr--toolbar
        a
            margin 0 2px

        a.vw-btn-separator
            display none
</style>
