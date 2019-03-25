<template>
  <span>

    <span
      v-if="activator"
      ref="activator"
      class="activator"
      @click="!active && show()"
    >
      <slot name="activator" />
    </span>

    <span
      v-if="childrenActivator"
      ref="activator"
      class="activator enter"
      @click="!active && enter()"
    >
      <slot name="children-activator" />
    </span>

    <ul
      ref="items"
      class="items"
      :class="{ active : active, hidden: hidden }"
    >

      <span
        v-if="childrenActivator"
        ref="activator"
        class="activator leave"
        @click="active && leave()"
      >
        <slot name="children-activator" />
      </span>

      <template
        v-for="item in items"
      >
        <ResponsiveDropdown
          v-if="item.items"
          :key="item.text"
          :items="item.items"
          @enter="hide"
          @leave="hide"
        >
          <li
            slot="children-activator"
            class="item node"
            @click="item.action && item.action()"
          >
            <span class="lsaquo">
              &lsaquo;
            </span>
            <p>
              {{ item.text }}
            </p>
            <span class="rsaquo">
              &rsaquo;
            </span>
          </li>
        </ResponsiveDropdown>

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
  name: 'ResponsiveDropdown',
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
      hidden: false,
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
    show() {
      const { items, activator } = this.$refs;
      this.active = true;

      this.$nextTick(() => {
        items.style.top = `${activator.getBoundingClientRect().top + activator.getBoundingClientRect().height}px`;
      });
    },
    enter() {
      this.$emit('enter', true);
      this.active = true;
    },
    leave() {
      this.$emit('leave', false);
      this.active = false;
    },
    hide(value = false) {
      this.hidden = value;
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
    width: calc(100% - 2px);

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
          justify-content: center;
        }
    }

    &.active {
        display: inherit;
        visibility: visible;

        &.hidden {
          visibility: hidden;
        }
    }

    .activator {
      &.enter {
        .lsaquo {
            opacity: 0;
        }
        .rsaquo {
            opacity: 1;
        }
      }

      &.leave {
        .lsaquo {
          opacity: 1;
        }
        .rsaquo {
          opacity: 0;
        }
      }
    }
}
</style>
