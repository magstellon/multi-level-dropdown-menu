<template>
  <ResponsiveDropdown
    v-if="responsive"
    :items="items"
  >
    <slot
      slot="activator"
      name="activator"
    />
  </ResponsiveDropdown>
  <NominalDropdown
    v-else
    :items="items"
  >
    <slot
      slot="activator"
      name="activator"
    />
  </NominalDropdown>
</template>

<script>
import NominalDropdown from './NominalDropdown.vue';
import ResponsiveDropdown from './ResponsiveDropdown.vue';

const SMALL_BREAKPOINT = 640;

function validate(items, isValid = true) {
  let valid = isValid;

  items.forEach((item) => {
    // Text must be define
    if (!item.text) valid = false;
    // Either action or items
    if (typeof item.action !== 'undefined' && item.items) valid = false;
    // Action or items must be define
    if (typeof item.action === 'undefined' && !item.items) valid = false;

    if (item.items) valid = validate(item.items, valid);
  });

  return valid;
}

export default {
  name: 'Dropdown',
  components: {
    NominalDropdown,
    ResponsiveDropdown,
  },
  props: {
    items: {
      type: Array,
      required: true,
      validator: validate,
    },
  },
  data() {
    return {
      responsive: false,
    };
  },
  mounted() {
    this.resize();
    window.addEventListener('resize', this.resize);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resize);
  },
  methods: {
    resize() {
      this.responsive = window.innerWidth < SMALL_BREAKPOINT;
    },
  },
};
</script>
