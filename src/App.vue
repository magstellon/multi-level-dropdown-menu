<template>
  <div id="app">
    <Dropdown :items="items">
      <button
        slot="activator"
        class="outline purple-white"
      >
        Dropdown
      </button>
    </Dropdown>
  </div>
</template>

<script>
import Dropdown from './components/Dropdown.vue';

const nop = () => {};

export default {
  name: 'App',
  components: {
    Dropdown,
  },
  data() {
    return {
      items: [
        {
          text: 'Mon Compte',
          // eslint-disable-next-line
          action: () => alert('You have an trusted account'),
        },
        {
          text: 'Langue',
          items: [
            {
              text: 'Français',
              items: [
                {
                  text: 'Belge',
                  // eslint-disable-next-line
                  action: () => alert('Belge une fois !'),
                },
                {
                  text: 'Canadien',
                  items: [
                    {
                      text: 'Nord',
                      action: nop,
                    },
                    {
                      text: 'Sud',
                      action: nop,
                    },
                  ],
                },
              ],
            },
            {
              text: 'Anglais',
              action: nop,
            },
            {
              text: 'Espagnol',
              items: [
                {
                  text: 'Catalan',
                  action: nop,
                },
                {
                  text: 'Argentine',
                  action: nop,
                },
              ],
            },
          ],
        },
        {
          text: 'A propos',
          items: [
            {
              text: 'Contact',
              action: nop,
            },
            {
              text: 'Qui sommes nous ?',
              action: nop,
            },
            {
              text: 'Lorem ipsum dolor sit amet',
              action: nop,
            },
          ],
        },
      ],
    };
  },
};
</script>

<style lang="scss">
body {
  background-color: #ECF1EF;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  font-size: 13px;
  margin-top: 60px;
}
</style>
