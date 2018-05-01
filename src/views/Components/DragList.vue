<template>
    <drag-item :list="list" :options="options" @change="changeHandler">
        <div class="board-item" v-for="element in list" :key="element.id">
            <slot :element="element" />
        </div>
        <div class="board-blank" v-if="list.length === 0">
            <slot name="blank-text"></slot>
        </div>
    </drag-item>
</template>
<script>
import dragItem from 'vuedraggable';

export default {
    name: 'dragList',
    components: {
        dragItem
    },
    props: {
        headerText: {
            type: String,
            default: 'Header'
        },
        options: {
            type: Object,
            default() {
                return {};
            }
        },
        list: {
            type: Array,
            default() {
                return [];
            }
        },
        changeHandler: {
            type: Function
        }
    }
};
</script>
<style lang="scss">
.board-item {
    width: 100%;
    margin: 3px 0;
}
.board-blank {
    display: none;

    &:only-child {
        display: unset;
    }
}
</style>

