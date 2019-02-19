<template>

  <component :is="type" class="library">

    <main role="main">

      <template v-if="primaryCTA">
        <call-to-action :action="primaryCTA.acf.action"
                        :copy="primaryCTA.acf.copy"
                        :image="primaryCTA.acf.image"
                        :heading="primaryCTA.acf.heading"
                        :link="primaryCTA.acf.link"></call-to-action>
      </template>

      <section class="background--blue-alternate library__section pb-3 pl-md-2 pb-md-4 pt-md-4 pr-md-2">

        <div class="m-md-auto d-md-flex flex-md-wrap flex-xl-nowrap justify-content-md-between col-md-10 pl-md-0 pr-md-0 pt-4 pb-4">

          <div class="card card--background-blue-dark mb-3 mb-xl-0 col-md-12 col-xl-4 mr-xl-3 p-4">

            <event-card class="card--background-blue-alternate"
                        :event="randomEvent"
                        heading-class="h4 text--white text--bold mt-4"
                        :key="randomEvent.id"
                        :truncate-excerpt="true"
                        v-if="randomEvent "/>

            <card content-container-class="p-3"
                  content-type="service"
                  :copy="randomServiceItem.description"
                  :heading="randomServiceItem.name"
                  v-if="randomServiceItem">

              <template slot="action">

                <router-link class="button button--orange" :to="`/services/${randomServiceItem.slug}`">
                  {{ randomServiceItem.acf.button_text ? randomServiceItem.acf.button_text : 'Explore'}}
                </router-link>

              </template>

            </card>

          </div>

          <collection-item class="card--background-blue-dark col-md-6 col-xl-8 mb-xl-0"
                           :item="randomCollectionItem"
                           heading-level="h3"
                           subheading-class="mt-1 text--white"
                           subheading-level="h4"
                           variant="feature"
                           v-if="randomCollectionItem" />

        </div>

      </section>

      <section class="background--white library__section p-3">

        <div class="col-md-10 d-md-flex m-auto pb-4 pl-0 pr-0 pt-4">

          <div class="col-md-8 p-0">

            <Showcase collection-link="/collection"
                      :collection-items="collection"
                      heading="New this week" />

            <card class="card--background-white text--dark"
                  content-type="blog"
                  :explainer="post.author.nice_name"
                  :sub-explainer="post.date | moment('dddd, MMMM Do')"
                  :heading="post.title"
                  v-if="post">

              <div slot="copy">
                <div v-html="post.excerpt"></div>
              </div>

              <template slot="action">
                <router-link class="button button--aqua" :to="`/posts/${post.slug}`">
                  Info
                </router-link>
              </template>

            </card>

          </div>

          <div class="col-4 p-0" v-if="secondaryCTA">
            <template>
              <call-to-action :action="secondaryCTA.acf.action"
                              :copy="secondaryCTA.acf.copy"
                              :image="secondaryCTA.acf.image"
                              :heading="secondaryCTA.acf.heading"
                              :link="secondaryCTA.acf.link"></call-to-action>
            </template>
          </div>


        </div>

      </section>

    </main>

  </component>

</template>

<script>
import CallToAction from "../patterns/CallToAction.vue";
import Card from "../patterns/Card.vue";
import CollectionItem from '../patterns/CollectionItem.vue';
import EventCard from '../patterns/EventCard.vue';
import Showcase from '../patterns/Showcase.vue';

/**
 * Shows how to layout and structure a home page.
 */
export default {
  name: "Index",

  status: "prototype",

  release: "1.0.0",

  components: {
    CallToAction,
    Card,
    CollectionItem,
    EventCard,
    Showcase,
  },

  computed: {
    callsToAction() {
      return this.$store.getters.getContentByService(
        'callsToAction',
        'any',
        this.location,
      ).sort((a,b) => (a.acf.priority > b.acf.priority) ? 1
                    : ((b.acf.priority > a.acf.priority) ? -1 
                    : (a.acf.expire > b.acf.expire) ? -1 
                    : (b.acf.expire > a.acf.expire) ? 1:  0));
    },

    collection() {
      return this.$store.state.collection;
    },

    events() {
      return this.$store.state.events;
    },

    post() {
      return this.$store.state.posts[0];
    },

    primaryCTA(){
      return this.callsToAction[0];
    },

    randomCollectionItem() {
      const collectionItemsWithAbstracts = this.collection
        .filter(item => item.acf.abstract !== '' && item.acf.abstract.length < 400);
      return collectionItemsWithAbstracts[
        Math.floor(Math.random() * collectionItemsWithAbstracts.length)
      ];
    },

    randomEvent() {
      return this.events[
        Math.floor(Math.random() * this.events.length)
      ];
    },

    randomServiceItem() {
      const servicesWithDescription = this.services
        .filter(service => service.description !== '');
      return servicesWithDescription[
        Math.floor(Math.random() * servicesWithDescription.length)
      ];
    },

    secondaryCTA(){
      let ctas = this.callsToAction;
      /*
      * if there's no more 'location specific' content,
      * let's look for regional content & filter out primaryCTA
      * then return a random item for the secondary CTA.
      */
      if(ctas.length < 2){
        ctas = this.$store.state.callsToAction.filter(
          page => (!page.acf.location ||
              page.acf.location == false ||
              page.acf.location.some(
                location => location.slug === 'headquarters')) &&
                page.id !== ctas[0].id
        ).sort(
          (a,b) => (a.acf.priority > b.acf.priority) ? 1
                    : ((b.acf.priority > a.acf.priority) ? -1 
                    /* : (a.acf.expire > b.acf.expire) ? -1 
                    : (b.acf.expire > a.acf.expire) ? 1 */:  0));
      } else {
        ctas.shift();
      }
      /**
       * Check if they're all equally prioritized, if so return a random item.
       */
      const priorities = Array.from(new Set(ctas.map(item => item.acf.priority)))
        .map(priority => {
          return { 
            item: ctas.find(item => item.acf.priority === priority ).acf.priority};
      });

      let num = priorities.length < 2 ? Math.floor(Math.random() * ctas.length) : 0;

      return ctas[num];
    },

    services() {
      return this.$store.state.services;
    },    
  },

  props: {
    location: {
      type: String
    },
    /**
     * The html element name used for the component.
     */
    type: {
      type: String,
      default: 'div',
    },
  },
};
</script>


<docs>
  ```jsx
  <index />
  ```
</docs>
