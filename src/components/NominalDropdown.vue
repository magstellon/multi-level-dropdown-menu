<template>
  <span @mouseleave="active && leave()">

    <span
      v-if="activator"
      ref="activator"
      class="activator"
      @click="!active && click()"
    >
      <slot name="activator" />
    </span>

    <span
      v-if="childrenActivator"
      ref="activator"
      class="activator"
      @mouseover="!active && enter()"
    >
      <slot name="children-activator" />
    </span>

    <ul
      ref="items"
      class="items"
      :class="{ active : active}"
    >
      <template
        v-for="item in items"
      >
        <NominalDropdown
          v-if="item.items"
          :key="item.text"
          :items="item.items"
        >
          <li
            slot="children-activator"
            class="item node"
            @click="item.action && item.action()"
          >
            <p>
              {{ item.text }}
            </p>
            <span class="rsaquo">
              &rsaquo;
            </span>
          </li>
        </NominalDropdown>

        <li
          v-else
          :key="item.text"
          class="item leaf"
          @click="item.action && item.action()"
        >
          <p>{{ item.text }}</p>
        </li>
      </template>
    </ul>

  </span>
</template>

<script>

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
  name: 'NominalDropdown',
  props: {
    items: {
      type: Array,
      required: true,
      validator: validate,
    },
  },
  data() {
    return {
      active: false,
      responsive: false,
    };
  },
  computed: {
    activator() {
      return !!this.$slots.activator;
    },
    childrenActivator() {
      return !!this.$slots['children-activator'];
    },
  },
  methods: {
    click() {
      const { items, activator } = this.$refs;
      this.active = true;

      this.$nextTick(() => {
        items.style.top = `${activator.getBoundingClientRect().top + activator.getBoundingClientRect().height}px`;
        items.style.left = `${activator.getBoundingClientRect().left}px`;
      });
    },
    enter() {
      const { items, activator } = this.$refs;
      this.active = true;

      this.$nextTick(() => {
        items.style.top = `${activator.getBoundingClientRect().top - items.getBoundingClientRect().top - 1}px`;
        items.style.left = `${activator.getBoundingClientRect().width + 1}px`;
      });
    },
    leave() {
      const { items } = this.$refs;
      this.active = false;

      items.style.top = 0;
      items.style.left = 0;
    },
  },
};
</script>

<style lang="scss" scoped>
@import '../../node_modules/reset-css/sass/reset';

.items {

    background-color: #fff;
    border: 1px solid rgba(0,0,0,.15);
    border-bottom: 0px;
    color: #212529;
    position: absolute;
    top: 0;
    left: 0;
    padding: 0;
    margin: 0;
    display:none;
    white-space:nowrap;

    .items, .item {
        list-style: none;
    }

    .item {
        display: flex;
        flex-wrap: nowrap;
        justify-content: space-between;
        cursor: pointer;
        transition: all 0.4s ease;
        border-bottom: 1px solid #e2e4e3;

        &.active {
          > .items {
              display: inherit;
          }
        }

        &:hover {
          background-color: #f8f9fa;
        }

        * {
          text-align: left;
        }

        &.node {
          padding: 8px 0 8px 16px;

          .rsaquo {
            margin-right: 16px;
            margin-left: 8px;
          }
        }

        &.leaf {
          padding: 8px 16px 8px 16px;
        }
    }

    &.active {
        display: inherit;
    }
}
</style>
