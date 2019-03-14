<template>

    <main class="page" role="main">

         <template v-if="pageObject">

            <header class="background--white d-flex p-4">

                <div class="col-md-10 m-auto">

                    <div class="col-md-8">

                        <heading class="channel__title text--dark text--serif"
                                 level="h1" v-html="pageObject.title.rendered">
                        </heading>

                    </div>

                </div>

            </header>

            <section class="background--white library__section p-3">

                <div v-html="pageObject.content.rendered"></div>

            </section>

        </template>

    </main>

  </template>

<script>
import Heading from '../elements/Heading.vue';

export default {
  name: 'Page',

  components: {
    Heading,
  },

  props: {
    pageObject: {
      type: Object,
    },
  },
};
</script>
