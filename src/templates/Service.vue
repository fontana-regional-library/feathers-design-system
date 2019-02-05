<template>
    <main class="service" role="main">

        <template v-if="serviceObject">

            <template v-for="(call, index) in serviceCallsToAction" v-if="index === 0">
                <call-to-action :action="call.acf.action"
                                :copy="call.acf.copy"
                                :image="call.acf.image"
                                :heading="call.acf.heading"
                                :link="call.acf.link"></call-to-action>
            </template>

            <header class="background--white d-flex p-4">

                <div class="col-md-10 m-auto">

                    <div class="col-md-8">

                        <heading class="channel__title text--dark text--serif"
                                 level="h1" v-html="serviceObject.name">
                        </heading>

                        <p class="channel__description" v-html="serviceObject.description">
                        </p>

                    </div>

                </div>

            </header>

            <section class="background--white library__section p-3">

                <div class="col-lg-10 m-lg-auto">

                    <div class="d-md-flex">

                        <div class="co col-md-6 col-lg-4"></div>

                        <div class="col col-lg-8">

                            <Showcase v-if="serviceCollection.length > 0"
                                      :collection-items="serviceCollection"
                                      heading="Related Materials" />

                            <template v-for="event in serviceEvents">

                                <event-card class="card--background-gray"
                                            :event="event"
                                            :key="event.id"
                                            v-if="event" />

                            </template>

                             <template v-for="page in servicePages">
                                <card badge-label="Information"
                                  :heading="page.title.rendered"
                                  class="card--background-white text--dark"
                                  content-type="blog">
                                  <div slot="copy">
                                        {{ getExcerpt(page.content.rendered) }}
                                    </div>

                                <template slot="action">
                                    <router-link class="button button--aqua" :to="`/${page.slug}`">More</router-link>
                                </template>

                            </card>
                            </template>

                            <template v-for="article in serviceArticles">
                                <card badge-label="Article"
                                  :heading="article.title.rendered"
                                  class="card--background-gray"
                                  content-type="blog"
                                  :explainer="article.date | moment('dddd, MMMM Do')">
                                  <div slot="copy">
                                        {{ getExcerpt(article.content.rendered) }}
                                    </div>

                                <template slot="action">
                                    <router-link class="button button--orange" :to="`/articles/${article.slug}`">More</router-link>
                                </template>

                            </card>
                            </template>

                            <template v-for="item in serviceCollection">

                                <collection-item class="card--background-blue-dark"
                                                 :item="item"
                                                 heading-level="h3"
                                                 :key="item.id"
                                                 subheading-class="mt-1 text--white"
                                                 subheading-level="h4"
                                                 variant="feature"
                                                 v-if="item" />
                            </template>

                        </div>
                    </div>

                </div>

            </section>

        </template>

    </main>
</template>

<script>
import CallToAction from '../patterns/CallToAction.vue';
import Card from '../patterns/Card.vue';
import CollectionItem from '../patterns/CollectionItem.vue';
import EventCard from '../patterns/EventCard.vue';
import Heading from '../elements/Heading.vue';
import Showcase from '../patterns/Showcase.vue';

export default {
  name: 'Service',

  components: {
    CallToAction,
    Card,
    CollectionItem,
    EventCard,
    Heading,
    Showcase,
  },
  computed: {
    blog(){},
    serviceCallsToAction() {
      return this.fetchData('callsToAction');
    },

    serviceCollection() {
      return this.fetchData('collection');
    },

    servicePages() {
      return this.fetchData('pages');
    },
    serviceArticles() {
      return this.fetchData('articles');
    },
    serviceEvents() {
      return this.fetchData('events');
    },
  },

  methods: {
     fetchData(type){
      let content = this.$store.getters.getContentByService(
        type,
        this.serviceObject.slug,
        this.location,
      );
      if(content.length == 0 || content.length < 6){
        let query = { urlParams:`&services=${this.serviceObject.id}`, contentType:type};
        this.$store.dispatch('getMoreContent', query);
      }
      return content.length < 6 ? this.$store.getters.getContentByService(
        type,
        this.serviceObject.slug,
        this.location,
      ) : content;
    },
    getAuthor(authorId) {
      let author = this.$store.getters.getAuthorById(Number(authorId));
      if(!author){
        
      }
      return author.name;
    },
    getExcerpt(excerpt) {
      const excerptContainer = document.createElement('div');
      excerptContainer.innerHTML = excerpt;
      let content = excerptContainer.value;
      return excerptContainer.textContent.length > 500
        ? `${excerptContainer.textContent.substring(0, 500)} ...`
        : excerptContainer.textContent;
    },
  },

  props: {
    serviceObject: {
      type: Object,
    },
  },
};
</script>

<style lang="scss">
.event {
  font-family: $font-family-text;

  &__excerpt {
    color: $color-blue-alternate;
    font-family: $font-family-serif;
    font-size: $font-size-base;

    @media #{$media-query-medium} {
      font-size: $font-size-large;
    }
  }

  &__description {
    color: $color-blue-alternate;
    font-family: $font-family-text;
    font-size: $font-size-base;
  }
}
.heading__separator {
  display: flex;
  justify-content: flex-start;
  align-items: stretch;
  height: 8px;
  border: none;
  box-shadow: none;
  margin: 40px 0;

  &:before {
    flex: 0 1 144px;
    background-color: $color-blue-alternate;
    content: "";
  }
}

.separator {
  display: flex;
  justify-content: flex-start;
  align-items: stretch;
  height: 4px;
  border: none;
  box-shadow: none;
  margin: 40px 0;
}
</style>
