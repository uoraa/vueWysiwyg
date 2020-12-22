<template>
    <div v-if="addedhtml">
        <!-- <button type="button" @click="blockquote ">Wrap in blockquote</button> -->
        <!-- <button @click="getstuff">getstuff</button> -->
        <button v-for="(html, idx) in addedhtml" :key="idx" @click="addTemplate(html)">{{ html.name }}</button>
    </div>
</template>

<script>

export default {
    title: "template",
    icon: '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-bookshelf" viewBox="0 0 16 16"><path fill-rule="evenodd" d="M2.5 0a.5.5 0 0 1 .5.5V2h10V.5a.5.5 0 0 1 1 0v15a.5.5 0 0 1-1 0V15H3v.5a.5.5 0 0 1-1 0V.5a.5.5 0 0 1 .5-.5zM3 14h10v-3H3v3zm0-4h10V7H3v3zm0-4h10V3H3v3z"/></svg>',
    addedhtml: null,
    created() {
        if (this.$parent.$options.propsData.options.templates !== undefined) {
            var templateHtml = this.$parent.$options.propsData.options.templates || [];
            if (templateHtml.length > 0) {
                this.addedhtml = templateHtml;
            }
        }
    },
    methods: {
        getstuff (e){
            console.log(this.$parent.$options.propsData.options);
        },
        addTemplate (myHtml) {
            this.$parent.closeDashboard();
            this.$emit("exec", "insertHTML", `${myHtml.html}`);
        },
        blockquote (e){
            this.$parent.closeDashboard();

            // EXAMPLES

            // wrap selection with a tag
            // this.$emit("exec", "formatBlock", "blockquote");

            // insert html
            // this.$emit("exec", "insertHTML", `<a href='${this.url}'>${this.title}</a>`);
        }
    }
};

</script>
