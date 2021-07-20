<template>
  <div class="stepper">
    <div class="top">
      <div
        v-for="(item, index) in options.headers"
        :class="{
          'step-header': true,
          active: index <= currentPosition,
          start: index === 0,
          end: index === options.headers.length,
        }"
        :key="index"
      >
        <div class="header-indicator">
          <div class="step-header-line" v-if="index > 0"></div>
          <div class="step-header-content" @click="slideTo(index)">
            {{ index + 1 }}
          </div>
        </div>
        <div class="title" :class="{ title: true }">
          {{ item.title }}
        </div>
      </div>
    </div>

    <div style="width:100%">
      <transition-group :name="transitionType" class="body" mode="out-in">
        <div
          v-for="(item, index) in options.headers"
          :key="'step' + index"
          v-show="currentPosition === index"
          :class="{ 'steps-item': true }"
        >
          <slot :name="'step-' + (index + 1)"></slot>
        </div>
      </transition-group>
    </div>
    <div class="foot">
      <button
        v-if="currentPosition > 0"
        class="prev-button"
        type="button"
        @click="prev()"
      >
        {{ options.prevText ? options.prevText : "Prev" }}
      </button>
      <button
        v-if="currentPosition < options.headers.length - 1"
        class="next-button"
        type="button"
        @click="next()"
      >
        {{ options.nextText ? options.nextText : "Next" }}
      </button>
      <button
        v-if="currentPosition >= options.headers.length - 1"
        class="next-button goto"
        type="button"
        @click="goTo()"
      >
        {{ options.goToText ? options.goToText : "Go to" }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Stepper",
  props: ["options"],
  data() {
    return {
      currentPosition: 0,
      transitionType: "slide",
    };
  },
  methods: {
    goTo() {
      if (this.options.dispatch) {
        this.options.payload.step = this.currentPosition;
        this.$store
          .dispatch(this.options.dispatcher, this.options.payload)
          .then(() => this.$router.push(this.options.router));
      }

      if (this.options.router) {
        return this.$router.push(this.options.router);
      }
    },
    slide_dispatch(index) {
      this.options.payload.step = this.currentPosition;
      this.$store
        .dispatch(this.options.dispatcher, this.options.payload)
        .then(() => {
          this.currentPosition = index;
        });
    },

    next_dispatch() {
      this.options.payload.step = this.currentPosition;
      this.$store
        .dispatch(this.options.dispatcher, this.options.payload)
        .then(() => {
          this.currentPosition++;
        });
    },
    prev_dispatch() {
      this.options.payload.step = this.currentPosition--;
      this.$store
        .dispatch(this.options.dispatcher, this.options.payload)
        .then(() => {
          this.currentPosition--;
        });
    },
    next() {
      if (this.currentPosition < this.options.headers.length - 1) {
        this.transitionType = "stepper-slide-1";

        if (this.options.dispatch) {
          this.next_dispatch();
        } else {
          this.currentPosition++;
        }
      }
    },
    prev() {
      if (this.currentPosition > 0) {
        this.transitionType = "stepper-slide-2";
        if (this.options.dispatch) {
          this.prev_dispatch();
        } else {
          this.currentPosition--;
        }
      }
    },
    slideTo(index) {
      if (this.currentPosition === index) return;
      if (this.currentPosition > index) {
        this.transitionType = "stepper-slide-2";
      } else {
        this.transitionType = "stepper-slide-1";
      }
      this.slide_dispatch(index);
      //this.currentPosition = index;
    },
  },
};
</script>
<style src="./Stepper.css" scoped></style>
<style scoped></style>
