<template>
  <div class="thuong-datepicker">
    <div
      class="thuong-datepicker__wrapper"
      :class="{ focus: onFocus, 'disabled-input': disabled }"
    >
      <input
        type="text"
        class="thuong-datepicker__input"
        :placeholder="placeholder"
        :ref="refs"
        v-model="datetime"
        :disabled="disabled"
        :style="width ? 'width: ' + width + 'px' : ''"
        @focus="focus"
        @blur="blur"
        @keydown.prevent="preventInput"
      />

      <span class="thuong-datepicker__icon-right" @click="toggleDatepicker">
        <svg
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 512 512"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          enable-background="new 0 0 512 512"
        >
          <g>
            <g>
              <path
                d="M490.6,43H381.1V11h-20.9v32H151.7V11h-20.9v32H21.4C15.2,43,11,47.2,11,53.6v436.7c0,6.4,4.2,10.7,10.4,10.7h469.1    c6.3,0,10.4-4.3,10.4-10.7V53.6C501,47.2,496.8,43,490.6,43z M480.1,479.7H31.9V64.3h99v32h20.9v-32h208.5v32h20.9v-32h99V479.7z"
              />
              <path
                d="m84,170.8c-6.3,0-10.4,4.3-10.4,10.7v245c0,6.4 4.2,10.7 10.4,10.7h344c6.3,0 10.4-4.3 10.4-10.7v-245c0-6.4-4.2-10.7-10.4-10.7h-344zm333.6,245h-323.2v-223.7h323.2v223.7z"
              />
              <rect width="20.9" x="214.3" y="232.6" height="21.3" />
              <rect width="20.9" x="276.9" y="232.6" height="21.3" />
              <rect width="20.9" x="339.4" y="232.6" height="21.3" />
              <rect width="20.9" x="151.7" y="293.3" height="21.3" />
              <rect width="20.9" x="214.3" y="293.3" height="21.3" />
              <rect width="20.9" x="276.9" y="293.3" height="21.3" />
              <rect width="20.9" x="339.4" y="293.3" height="21.3" />
              <rect width="20.9" x="151.7" y="355.1" height="21.3" />
              <rect width="20.9" x="214.3" y="355.1" height="21.3" />
              <rect width="20.9" x="276.9" y="355.1" height="21.3" />
              <rect width="20.9" x="339.4" y="355.1" height="21.3" />
            </g>
          </g>
        </svg>
      </span>
    </div>

    <ThuongPopupTable
      v-model="datetime"
      :locale="locale"
      v-if="openPopup && !disabled && this.mode == 'single'"
      @closePopup="openPopup = $event"
      @onSelect="onSelect"
      @onClear="onClear"
    />

    <ThuongPopupRange
      v-model="datetime"
      :locale="locale"
      v-if="openPopup && !disabled && this.mode == 'range'"
      @closePopup="openPopup = $event"
      @onSelect="onSelect"
      @onClear="onClear"
    />

    <ThuongPopupMultiple
      v-model="datetime"
      :locale="locale"
      v-if="openPopup && !disabled && this.mode == 'multiple'"
      @closePopup="openPopup = $event"
      @onSelect="onSelect"
      @onClear="onClear"
    />
  </div>
</template>

<script>
import ThuongPopupTable from "./ThuongPopupTable.vue";
import ThuongPopupRange from "./ThuongPopupRange.vue";
import ThuongPopupMultiple from "./ThuongPopupMultiple.vue";
export default {
  components: {
    ThuongPopupTable,
    ThuongPopupRange,
    ThuongPopupMultiple,
  },
  props: {
    value: {
      default: null,
      require: true,
    },
    placeholder: {
      type: String,
      default: "",
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    mode: {
      type: String,
      default: "single",
    },
    refs: {
      type: String,
      default: "thuong-datepicker__input",
    },
    width: {
      type: Number,
      require: false,
    },
  },
  data() {
    return {
      onFocus: false,
      datetime: null,
      openPopup: false,
      locale: "en-US",
    };
  },
  created() {
    this.datetime = this.value;
  },
  watch: {
    datetime: function (value) {
      if (value) {
        this.$emit("input", value);
      }
    },
  },
  methods: {
    focus() {
      this.onFocus = true;
      this.openPopup = true;
      this.$emit("onFocus");
    },
    blur() {
      this.onFocus = false;
      this.$emit("onBlur");
    },
    toggleDatepicker() {
      if (!this.disabled) {
        this.openPopup = !this.openPopup;
        this.$refs[this.refs].focus();
      }
    },
    preventInput() {
      return true;
    },
    onSelect(val) {
      this.$emit("onSelect", val);
    },
    onClear() {
      this.$emit("onClear");
    },
  },
};
</script>

<style lang="scss" scoped>
.thuong-datepicker {
  display: inline-block;
  position: relative;

  &__wrapper {
    border-radius: 4px;
    border: 1px solid #dcdcdc;
    padding: 6px 12px;
    transition: all 0.25s ease-in-out;
    display: inline-flex;
    justify-content: space-between;
    align-items: center;

    &.disabled-input {
      cursor: not-allowed;

      .thuong-datepicker__input {
        color: #888;
        cursor: not-allowed;
        width: 100%;
      }

      .thuong-datepicker__icon-right {
        color: #888;
        cursor: not-allowed;

        svg {
          fill: #888;
        }
      }
    }
  }

  .focus {
    border: 1px solid #16a085;
  }

  &__input {
    border: none;
    outline: 0;
    padding-right: 8px;
  }

  &__icon-right {
    cursor: pointer;
    margin-top: 2px;

    svg {
      width: 18px;
      height: 18px;
    }
  }
}
</style>
