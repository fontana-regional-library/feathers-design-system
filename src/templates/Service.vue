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

                        <div class="co col-md-6 col-lg-4">
                          <p>{{ total.events }}</p>
                          <p>{{ events.length }}</p>
                          <div v-if="showChannel">
                              <a class="button button button--pink"
                            v-if="pages.length < total.pages"
                              @click="getMoreContent('pages');">
                                See More Information
                            </a>
                            <a class="button button button--pink"
                            v-if="total.articles > articles.length"
                              @click="getMoreContent('articles');">
                                See More Articles
                            </a>
                            <a class="button button button--pink"
                            v-if="total.collection > collection.length"
                              @click="getMoreContent('collection');">
                                See More Collection Items
                            </a>
                            <a class="button button button--pink"
                            v-if="total.events > events.length"
                              @click="getMoreContent('events');">
                                See More Events
                            </a>
                        </div>
                        <div id="backToChannel" v-if="showChannel === false">
                          <a class="button button button--aqua"
                          @click="backToChannel();">
                            Back to {{ serviceObject.name }} Channel
                        </a></div>
                        <div v-if="content !== 'events'">
                          <h2>Upcoming Related Events</h2>
                          
                          <template v-for="event in serviceEvents">

                                <event-card class="card--background-gray"
                                            :event="event"
                                            :key="event.id"
                                            v-if="event" />

                            </template>
                            </div>
                        </div>

                        <div class="col col-lg-8">
                          <div id="content-channel" v-if="showChannel">
                            <Showcase v-if="serviceCollection.length > 0"
                                      :collection-items="serviceCollection"
                                      heading="Related Materials" />

                            

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
                            <!-- CONTENT-->
                            <div v-if="showChannel===false">
                              <div id="events" v-if="content === 'events'">
                                <template v-for="event in events">

                                  <event-card class="card--background-gray"
                                              :event="event"
                                              :key="event.id"
                                              v-if="event" />

                              </template>
                              </div>
                              <div id="articles" v-if="content === 'articles'">
                                <template v-for="article in articles">
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
                              </div>
                              <div id="pages" v-if="content === 'pages'">
                                <template v-for="page in pages">
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
                              </div>
                              <div id="collection" v-if="content === 'collection'">
                                <template v-for="item in collection">

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
                              
                              <a class="button button button--pink"
                            v-if="content === 'pages' && pages.length < total.pages"
                              @click="getMoreContent('pages');">
                                Load More Information
                            </a>
                            <a class="button button button--pink"
                            v-if="content === 'articles' && total.articles > articles.length"
                              @click="getMoreContent('articles');">
                                Load More Articles
                            </a>
                            <a class="button button button--pink"
                            v-if="content === 'collection' && total.collection > collection.length"
                              @click="getMoreContent('collection');">
                                Load More Collection Items
                            </a>
                            <a class="button button button--pink"
                            v-if="content === 'events' && total.events > events.length"
                              @click="getMoreContent('events');">
                                Load More Events
                            </a>
                        
                            </div>
                            <!-- END CONTENT -->
                           


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
import {urls} from "../variables.js";

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
      if(this.contentData.callsToAction === 0){
        this.fetchData('callsToAction');
      }
       return this.$store.getters.getContentByService(
          'callsToAction',
          this.serviceObject.slug,
          this.location,
        );
    },

    serviceCollection() {
      if(this.contentData.collection === 0){
      this.fetchData('collection');
      }
       return this.$store.getters.getContentByService(
          'collection',
          this.serviceObject.slug,
          this.location,
        );
    },

    servicePages() {
      if(this.contentData.pages === 0){
      this.fetchData('pages');
      }
       return this.$store.getters.getContentByService(
          'pages',
          this.serviceObject.slug,
          this.location,
        );
    },
    serviceArticles() {
      if(this.contentData.articles === 0){
       this.fetchData('articles');
      }
       return this.$store.getters.getContentByService(
          'articles',
          this.serviceObject.slug,
          this.location,
        );
    },
    serviceEvents() {
      if(this.contentData.events === 0){
       this.fetchData('events');
      }
      
       return this.$store.getters.getContentByService(
          'events',
          this.serviceObject.slug,
          this.location,
        );
    },
  },
  data() {
    return {
      selectedDate: null,
      contentData: {
        callsToAction:0,
        articles:0,
        pages:0,
        collection:0,
        events:0,
        per_page:20,
      },
      total:{
        callsToAction:0,
        articles:0,
        pages:0,
        collection:0,
        events:0,
      },
      articles:[],
      callsToAction:[],
      pages:[],
      collection:[],
      events:[],
      showChannel: true,
      content: null,
    };
  },
  methods: {
    backToChannel(){
      this.showChannel = true;
      this.content = null;
    },
     fetchData(type){
       let page = this.contentData[type]+1;
       let fetchUrl = urls[type] + `&services=${this.serviceObject.id}&page=${page}`;
       if(this.contentData[type]===0){
       axios.get(fetchUrl)
      .then((response) =>{
        this.total[type] = response.headers['x-wp-total'];
        for(let i = 0; i<response.data.length; i++){
          const index = this[type].findIndex(item => item.id === response.data[i].id)
          if (index === -1){ 
            this[type].push(response.data[i]);
          }
        }      
        let payload = {'content': response.data, 'contentType': type};
        this.$store.commit('addMoreContent', payload);
        this.contentData[type]++;
      })
      .catch( (error)=>{
        console.log(error);
      });
       }
    },
    getMoreContent(type) {
      let page = this.contentData[type]+1;
      let fetchUrl = urls[type] + `&services=${this.serviceObject.id}&page=${page}`;
      axios.get(fetchUrl)
      .then((response) =>{
        this[type]= this[type].concat(response.data);
        this.contentData[type]++;
      })
      .catch( (error)=>{
        console.log(error);
      });
      
      this.showChannel = false;
      this.content = type;      
    },
    getAuthor(authorId) {
      let author = this.$store.getters.getAuthorById(Number(authorId));
      if(!author){
        this.fetchAuthors(authorId);
        author = this.$store.getters.getAuthorById(Number(authorId));
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