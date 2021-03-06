# Goal

We want two custom elements `<fc-flash-card>` and `<fc-practice-set>`. 
The user should be able to use these with a single `<script>` tag (and if necessary, one more for the framework ).
Module bundlers like webpack or browserify or even npm should NOT be required. Our goal is to make this usable
in all frameworks and apps such as WordPress, Rails etc.

I decided to use VueJS for this and built `FlashCard` and `PracticeSet` as components. These are working well as
Vue components, but when we convert these to WebComponents, `$children` is coming as empty so `PracticeSet`'s
`setChildVisibility()` method can't do its job. This is why I wrote `CardSet` which needs the hosting site to
manually create a Vue component and pass cards via `data` property. When the underlying issue is solved, we may
finish the WebComponent version. See this StackOverflow question for details: https://stackoverflow.com/questions/61506239/vuejs-children-is-empty-when-using-slots-in-the-webcomponent-build

`PracticeSet` and `CardSet` are almost like a carousel, except it only shows the first item among the children
elements, it lets the user (a) remove the first element - by clicking on green button or (b) move it from first
place to last place by clicking on the red button.

We need to write these two components in a way that these work correctly both as VueComponents and WebComponents.

A few questions about `PracticeSet`:

- How should FlashCard invoke the parent PracticeSet element so that it works in both environments?  Currently it
invokes a custom "answered" event. See FlashCard.vue#176
- Why do we need to use `mounted()`, `setChildVisibility()`, `this.$forceUpdate()` etc while using slots with arbitrary number of children elements? I feel I'm not using the right VueJS constructs to make this work well. Some ideas were taken from https://github.com/SSENSE/vue-carousel/blob/master/src/Carousel.vue but the approach is too dependent on VueJS implementation. Can we read the slots to produce a `data` property and then operate on that instead? One gotcha is that `<slot>` in `<template>` also become visible.