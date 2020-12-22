<template>
    <div v-if="addedhtml">
        <!-- <button type="button" @click="blockquote ">Wrap in blockquote</button> -->
        <!-- <button @click="getstuff">getstuff</button> -->
        <button v-for="(html, idx) in addedhtml" :key="idx" @click="addMyHtml(html)">{{ html.name }}</button>
    </div>
</template>

<script>

export default {
    title: "shorthtml",
    icon: '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-intersect" viewBox="0 0 16 16"><path fill-rule="evenodd" d="M0 2a2 2 0 0 1 2-2h8a2 2 0 0 1 2 2v2h2a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2v-2H2a2 2 0 0 1-2-2V2zm5 10v2a1 1 0 0 0 1 1h8a1 1 0 0 0 1-1V6a1 1 0 0 0-1-1h-2v5a2 2 0 0 1-2 2H5zm6-8H6a2 2 0 0 0-2 2v5H2a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1h8a1 1 0 0 1 1 1v2z"/></svg>',
    addedhtml: null,
    created() {
        if (this.$parent.$options.propsData.options.addShortHtml !== undefined) {
            var shortHtml = this.$parent.$options.propsData.options.addShortHtml || [];
            if (shortHtml.length > 0) {
                this.addedhtml = shortHtml;
            }
        }
    },
    methods: {
        getstuff (e){
            console.log(this.$parent.$options.propsData.options);
        },
        addMyHtml (myHtml) {
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

