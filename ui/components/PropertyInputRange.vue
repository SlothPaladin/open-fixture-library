<template>
  <span class="range">
    <Validate :state="formstate" tag="span">
      <PropertyInputNumber
        ref="firstInput"
        v-model="start"
        :name="`${name}-start`"
        :schema-property="schemaProperty.items"
        :minimum="rangeMin"
        :maximum="end !== `invalid` ? end : rangeMax"
        :required="required || rangeIncomplete"
        :hint="startHint"
        lazy
        @focus.native="onFocus($event)"
        @blur.native="onBlur($event)" />
    </Validate>
    …
    <Validate :state="formstate" tag="span">
      <PropertyInputNumber
        v-model="end"
        :name="`${name}-end`"
        :schema-property="schemaProperty.items"
        :minimum="start !== `invalid` ? start : rangeMin"
        :maximum="rangeMax"
        :required="required || rangeIncomplete"
        :hint="endHint"
        lazy
        @focus.native="onFocus($event)"
        @blur.native="onBlur($event)" />
    </Validate>
    {{ unit }}
  </span>
</template>

<script>
import { arrayProp, booleanProp, numberProp, objectProp, stringProp } from 'vue-ts-types';
import PropertyInputNumber from './PropertyInputNumber.vue';

export default {
  components: {
    PropertyInputNumber,
  },
  model: {
    prop: `range`,
  },
  props: {
    range: arrayProp().withDefault(null),
    name: stringProp().required,
    startHint: stringProp().withDefault(`start`),
    endHint: stringProp().withDefault(`end`),
    rangeMin: numberProp().optional,
    rangeMax: numberProp().optional,
    schemaProperty: objectProp().required,
    unit: stringProp().optional,
    required: booleanProp().withDefault(false),
    formstate: objectProp().required,
  },
  data() {
    return {
      validationData: {
        'complete-range': ``,
        'valid-range': ``,
      },
    };
  },
  computed: {
    start: {
      get() {
        return this.range ? this.range[0] : null;
      },
      set(startInput) {
        this.$emit(`input`, getRange(startInput, this.end));
        this.$emit(`start-updated`);
      },
    },
    end: {
      get() {
        return this.range ? this.range[1] : null;
      },
      set(endInput) {
        this.$emit(`input`, getRange(this.start, endInput));
        this.$emit(`end-updated`);
      },
    },
    rangeIncomplete() {
      return this.range && (this.start === null || this.end === null);
    },
  },
  mounted() {
    this.$emit(`vf:validate`, this.validationData);
  },
  methods: {
    /** @public */
    focus() {
      this.$refs.firstInput.focus();
    },
    onFocus(event) {
      this.$emit(`focus`);
    },
    onBlur(event) {
      if (!(event.target && event.relatedTarget) || event.target.closest(`.range`) !== event.relatedTarget.closest(`.range`)) {
        this.$emit(`blur`);
      }
    },
  },
};

/**
 * @param {number | null} start Start value of the range or null.
 * @param {number | null} end End value of the range or null.
 * @returns {[number, number] | null} Range array with the inputs or null if both inputs were null.
 */
function getRange(start, end) {
  if (start === null && end === null) {
    return null;
  }

  return [start, end];
}
</script>
