# vue-thuong-datepicker

> A Datepicker Component For Vue2

## Demo

<https://thuong-datepicker-example.web.app/>

![image](https://i.ibb.co/D4rJ0sR/thuong-datepicker.png)

## Install

```bash
$ npm i vue-thuong-datepicker --save
```

## Usage

Import in **main.js**

```js
import Vue from "vue";
import VueThuongDatepicker from "vue-thuong-datepicker";

Vue.use(VueThuongDatepicker);
...
```

**Usage**

```html
<template>
  <VueThuongDatepicker
    v-model="datetime"
    placeholder="hehehe"
    refs="inputDate"
    mode="range"
    @onFocus="onFocus"
    @onBlur="onBlur"
    @onSelect="onSelect"
    @onClear="onClear"
  />
</template>

<script>
  export default {
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
  };
</script>
```

### Props

| Prop        | Description                 | Type                | Default                      | Required |
| ----------- | --------------------------- | ------------------- | ---------------------------- | -------- |
| v-model     | value of datepicker         | String/Array/Object | null                         | true     |
| placeholder | placeholder of input        | String              |                              | false    |
| mode        | 'single'/'multiple'/'range' | String              | 'single'                     | false    |
| refs        | the ref of input            | String              | 'thuong-datepicker\_\_input' | false    |
| witdh       | the width of input (px)     | Number              |                              | false    |

### Example of v-model

If `range` is `'single'`, **v-model** can be a string or **null**.  
Case `multiple`, **v-model** can be an Array of date's string `[]` or **null**.  
Case `range`, **v-model** can be an Object like `{ from_date: '20/04/2022', to_date: '21/04/2022' }` or **null**.

### Events

| Name     | Description                     | Callback Arguments |
| -------- | ------------------------------- | ------------------ |
| onFocus  | When focusing on the input      |                    |
| onBlur   | When leaving focus on the input |                    |
| onSelect | When selecting a date           | date value         |
| onClear  | When clear the value            |                    |

Copyright (c) 2022 by ThuongND
