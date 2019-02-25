<template>
    <main class="background--white event" role="main">

        <article v-if="eventObject">

            <header class="col-11 col-md-10 col-lg-6 m-auto p-lg-4">

                <heading class="event__title text--dark text--serif">{{ eventObject.title }}</heading>

                <span class="event__time text--dark">
                    {{eventObject.start_date | moment("dddd, MMMM Do YYYY h:mm a")}} &mdash;
                    {{eventObject.end_date | moment("dddd, MMMM Do YYYY h:mm a")}}
                </span>

                <div class="heading__separator"></div>

                <p class="event__excerpt" v-if="eventObject.excerpt" v-html="eventObject.excerpt"></p>

                <add-to-calendar :title="eventObject.title"
                                 :location="eventObject.venue.venue"
                                 :start="new Date(`${eventObject.start_date}`)"
                                 :end="new Date(`${eventObject.end_date}`)"
                                 :details="eventObject.description"
                                 inline-template>

                    <div class="mb-3">

                        <google-calendar class="button button--blue-alternate" id="google-calendar">
                            Add to Google Calendar
                        </google-calendar>

                        <microsoft-calendar class="button button--blue-alternate" id="microsoft-calendar">
                            Add to Microsoft Live Calendar
                        </microsoft-calendar>

                    </div>
                </add-to-calendar>

                <template v-for="contact in eventObject.organizer">
                        <person :key="contact.id"
                                :name="contact.organizer"
                                :title="contact.description"
                                type="organizer"
                                :profileImage="{url: contact.image.url, w: contact.image.width, h: contact.image.height}"
                                contentContainerClass="col-6 mb-2">
                                </person>
                      </template>

            </header>

            <section class="background--gray event__description">

                <div class="col-11 col-md-10 col-lg-6 m-auto pt-4 pb-4 p-lg-4">

                    <img :src="eventObject.image.url" alt="">

                    <heading class="text--dark" level="h2">About</heading>

                    <div v-html="eventObject.description"></div>

                    <heading class="text--dark" level="h3">When</heading>
                    {{eventObject.start_date | moment("dddd, MMMM Do YYYY h:mm a")}}
                    &mdash; {{eventObject.end_date | moment("dddd, MMMM Do YYYY h:mm a")}}

                    <heading class="text--dark" level="h3">Where</heading>

                    <router-link class="d-block text--dark text--underline" :to="`/locations/${eventObject.venue.slug}`">{{ eventObject.venue.venue }}</router-link>
                    <span class="d-block">{{ eventObject.venue.address }}</span>
                    <span class="d-block">{{ eventObject.venue.city }}, {{ eventObject.venue.state }} {{ eventObject.venue.zip }}</span>
                    <span class="d-block">{{ eventObject.venue.phone }}</span>

                </div>
            </section>

        </article>

    </main>
</template>

<script>
import AddToCalendar from 'vue-add-to-calendar';
import Vue from 'vue';
import VueMoment from 'vue-moment';

import Person from '../patterns/Person.vue';
import CallToAction from '../patterns/CallToAction.vue';
import Heading from '../elements/Heading.vue';

Vue.use(AddToCalendar);
Vue.use(VueMoment);

export default {
  name: 'Event',

  components: {
    CallToAction,
    Heading,
    Person
  },

  computed: {
    author() {
      return this.$store.getters.getAuthorById(Number(this.eventObject.author));
    },
  },

  props: {
    eventObject: {
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
