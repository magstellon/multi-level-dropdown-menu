<template>
  <span
    :class="{ responsive: responsive }"
    @mouseleave="!responsive && active && leave()"
  >
    <!-- Responsive Dropdown -->
    <template
      v-if="responsive"
    >
      <span
        ref="activator"
        class="activator enter"
        @[action]="toggle"
      >
        <slot name="activator" />
      </span>

      <ul
        ref="items"
        class="items"
        :style="{ top : style.top }"
        :class="{ active : active, hidden: hidden }"
      >

        <span
          v-if="!rootActivator"
          ref="activator"
          class="activator leave"
          @[action]="active && leave()"
        >
          <slot name="activator" />
        </span>

        <template
          v-for="item in items"
        >
          <Dropdown
            v-if="item.items"
            :key="item.text"
            :items="item.items"
            action="click"
            @enter="hide"
            @leave="hide"
          >
            <li
              slot="activator"
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
          </Dropdown>

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
    </template>
    <!-- Large Screen Dropdown -->
    <template
      v-else
    >
      <span
        ref="activator"
        class="activator"
        @[action]="!active && enter()"
      >
        <slot name="activator" />
      </span>

      <ul
        ref="items"
        class="items"
        :style="style"
        :class="{ active : active }"
      >
        <template
          v-for="item in items"
        >
          <Dropdown
            v-if="item.items"
            :key="item.text"
            :items="item.items"
            action="mouseover"
          >
            <li
              slot="activator"
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
          </Dropdown>

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
    </template>
  </span>
</template>

<script>


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
  props: {
    items: {
      type: Array,
      required: true,
      validator: validate,
    },
    action: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      active: false,
      hidden: false,
      responsive: false,
      style: {
        top: 0,
        left: 0,
      },
    };
  },
  computed: {
    rootActivator() {
      return this.$parent.$options.name !== this.$options.name;
    },
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
    toggle() {
      if (this.active) {
        this.leave();
      } else {
        this.enter();
      }
    },
    enter() {
      const { items, activator } = this.$refs;
      this.$emit('enter', true);
      this.active = true;

      if (this.responsive && this.rootActivator) {
          this.style.top = `${activator.getBoundingClientRect().top + activator.getBoundingClientRect().height}px`;

      } else {
        // Because element need to be render to use getBoundingClientRect.
        this.$nextTick(() => {
          if (this.rootActivator) {
            this.style.top = `${activator.getBoundingClientRect().top + activator.getBoundingClientRect().height}px`;
            this.style.left = `${activator.getBoundingClientRect().left}px`;
          } else {
            this.style.top = `${activator.getBoundingClientRect().top - items.getBoundingClientRect().top - 1}px`;
            this.style.left = `${activator.getBoundingClientRect().width + 1}px`;            
          }
        });
      }
    },
    leave() {
      this.active = false;
      this.$emit('leave', false);

      this.style.top = 0;
      this.style.left = 0;
    },
    hide(value = false) {
      this.hidden = value;
    },
  },
  watch: {
    responsive() {
      this.leave();
    }
  }
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

.responsive {
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
}
</style>
