<script>
import Vue from "vue";
// Uncomment import and local "components" registration if library is not registered globally.
// import { VueThuongDatepickerSample } from '@/entry.esm';

export default Vue.extend({
  name: "ServeDev",
  // components: {
  //  VueThuongDatepickerSample,
  // }
  data() {
    return {
      datetime: null,
    };
  },
  methods: {
    onFocus() {
      console.log("focus");
    },
    onBlur() {
      console.log("blur");
    },
    onSelect(val) {
      console.log(val);
    },
    onClear() {
      console.log("clear");
    },
  },
});
</script>

<template>
  <div id="app">
    <vue-thuong-datepicker
      v-model="datetime"
      placeholder="placeholder here"
      locale="default"
      refs="inputDate"
      mode="range"
      @onFocus="onFocus"
      @onBlur="onBlur"
      @onSelect="onSelect"
      @onClear="onClear"
    />
  </div>
</template>
