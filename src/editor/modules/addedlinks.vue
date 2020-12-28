<template>
    <div>
        <!-- <button type="button" @click="blockquote ">Wrap in blockquote</button> -->
        <!-- <button @click="getstuff">getstuff</button> -->
        <button v-for="(link, idx) in addLinks" :key="idx" @click="addMyLink(link)">{{ link.name }}</button>
    </div>
</template>

<script>

export default {
    title: "addedlinks",
    description: "Provided links",
    icon: '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-link-45deg" viewBox="0 0 16 16"><path d="M4.715 6.542L3.343 7.914a3 3 0 1 0 4.243 4.243l1.828-1.829A3 3 0 0 0 8.586 5.5L8 6.086a1.001 1.001 0 0 0-.154.199 2 2 0 0 1 .861 3.337L6.88 11.45a2 2 0 1 1-2.83-2.83l.793-.792a4.018 4.018 0 0 1-.128-1.287z"/><path d="M6.586 4.672A3 3 0 0 0 7.414 9.5l.775-.776a2 2 0 0 1-.896-3.346L9.12 3.55a2 2 0 0 1 2.83 2.83l-.793.792c.112.42.155.855.128 1.287l1.372-1.372a3 3 0 0 0-4.243-4.243L6.586 4.672z"/></svg>',
    addLinks: [],
    created() {
        var links = this.$parent.$options.propsData.options.addedlinks || [];
        if (links.length > 0) {
            this.addLinks = links;
        }
    },
    methods: {
        // getstuff (e){
        //     console.log(this.$parent.$options.propsData.options.addedlinks);
        // },
        addMyLink (myLink) {
            this.$parent.closeDashboard();
            this.$emit("exec", "insertHTML", `<a href='${myLink.link}'>${myLink.name}</a>`);
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

