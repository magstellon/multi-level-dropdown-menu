<template>
    <ul class="items">
        <li class="item" 
            v-for="item in items" 
            :key="item.text"

            @mouseover="over"
            @mouseleave="leave"

            @click="item.action && item.action()"
        >
            <template v-if="item.items">
                <p>{{item.text}} &rsaquo;</p>
                <Dropdown :items="item.items" />
            </template>
            <p v-else>{{item.text}}</p>
        </li>
    </ul>
</template>

<script>

function getItem(element){
    return element.tagName === 'LI' ? element : element.parentElement;
}

function validate(items, valid = true){

    items.forEach(item => {
        // Text must be define
        if (!item.text) valid = false;
        // Either action or items
        if(typeof item.action !== 'undefined' && item.items) valid = false;
        // Action or items must be define
        if (typeof item.action === 'undefined' && !item.items) valid = false;
        
        if(item.items) valid = validate(item.items, valid);
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
    methods: {
        over(e){
            const item = getItem(e.target),
                children = item.querySelector('.items'),
                items = item.parentElement;

            // Activate the item hovered & Show children if any
            item.classList.add('active');

            // Position children
            if(children) {
                // Position is relative to items & border
                children.style.top = item.getBoundingClientRect().top - items.getBoundingClientRect().top - 2 + 'px';
                children.style.left = items.getBoundingClientRect().width + 'px';
            }
        },
        leave(e) {
            const item = getItem(e.target);
            
            // Hide the item hovered & children if any
            item.classList.remove('active')
        }
    }
}
</script>

<style lang="scss" scoped>
@import '../../node_modules/reset-css/sass/reset';

.items {

    background-color: #fff;
    border: 1px solid rgba(0,0,0,.15);
    color: #212529;
    position: absolute;
    top: 0;
    left: 0;
    padding: 0;
    margin: 0;

    .items, .item {
        list-style: none;
    }

    .item {
        padding: 0 10px;
        cursor: pointer;
        border-bottom: 1px solid #e2e4e3;
        padding: 4px 14px;
        transition: all 0.4s ease;

        &:last-child {
            border-bottom: none;
        }

        &.active {
            background-color: #f8f9fa;
            
            > .items {
                display: inherit;
            }
        }
    }

    .items {
        display:none;
        white-space:nowrap;
    }

}
</style>
