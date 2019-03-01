<template>
    <span @mouseleave="leave">

        <span
            v-if="activator"
            class="activator"
            ref="activator"
            @click="click">
            <slot name="activator"></slot>
        </span>

        <span
            v-if="childrenActivator"
            class="activator"
            ref="activator"
            @mouseover="over">
            <slot name="children-activator"></slot>
        </span>

        <ul
            class="items"
            ref="items">

            <template
                v-for="item in items">

                    <Dropdown
                        v-if="item.items"
                        :items="item.items"
                        :key="item.text">
                        <li
                            slot="children-activator"
                            class="item"
                            @click="item.action && item.action()">

                            <p>{{item.text}} &rsaquo;</p>

                        </li>
                    </Dropdown>

                    <li
                        v-else
                        class="item"
                        :key="item.text"
                        @click="item.action && item.action()">

                        <p>{{item.text}}</p>

                    </li>
            </template>
        </ul>

    </span>
</template>

<script>

function validate(items, valid = true) {
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
        }
    },
    data() {
        return {
            active: false,
        };
    },
    methods: {
        click() {
            const items = this.$refs.items,
                activator = this.$refs.activator;

                items.classList.add('active');

            items.style.top = activator.getBoundingClientRect().top + activator.getBoundingClientRect().height + 'px';
            items.style.left = activator.getBoundingClientRect().left + 'px';


        },
        over(){
            if (this.active) return;

            const items = this.$refs.items,
                activator = this.$refs.activator;

            items.classList.add('active');
    
            items.style.top = activator.getBoundingClientRect().top - items.getBoundingClientRect().top - 1 + 'px';
            items.style.left = activator.getBoundingClientRect().width + 1 + 'px';
            
            this.active = true;
        },
        leave() {
            const items = this.$refs.items;
    
            items.classList.remove('active');

            items.style.top = 0;
            items.style.left = 0;

            this.active = false;
        },
        rootPosition() {
            const items = this.$refs.items,
                activator = this.$refs.activator;

            return {
                top: activator.getBoundingClientRect().top + activator.getBoundingClientRect().height + 'px',
                left: activator.getBoundingClientRect().left + 'px',
            }
        },
        childrenPosition() {
            const items = this.$refs.items,
                activator = this.$refs.activator;

            return {
                top: activator.getBoundingClientRect().top - items.getBoundingClientRect().top - 1 + 'px',
                left: activator.getBoundingClientRect().width + 1 + 'px',
            }
        }
    },
    computed: {
        activator() {
            return !!this.$slots['activator']
        },
        childrenActivator(){
            return !!this.$slots['children-activator']
        }
    }
}
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
        padding: 0 10px;
        cursor: pointer;
        padding: 4px 14px;
        transition: all 0.4s ease;
        border-bottom: 1px solid #e2e4e3;

        &.active {
            background-color: #f8f9fa;
            
            > .items {
                display: inherit;
            }
        }
    }

    &.active {
        display: inherit;
    }
}
</style>
