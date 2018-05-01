<template>
    <div class="board">
        <anime-note :key="index" v-for="(note, index) in notes" :header-text="note.text" :day="note.name" />
    </div>
</template>
<script>
import AnimeNote from './AnimeNote.vue';

export default {
    name: 'anime',
    components: {
        AnimeNote
    },
    data: () => ({
        notes: [{
            text: '星期一',
            name: 'Mon'
        }, {
            text: '星期二',
            name: 'Tue'
        }, {
            text: '星期三',
            name: 'Wed'
        }, {
            text: '星期四',
            name: 'Thu'
        }, {
            text: '星期五',
            name: 'Fri'
        }, {
            text: '星期六',
            name: 'Sat'
        }, {
            text: '星期日',
            name: 'Sun'
        }]
    })
};
</script>
<style lang="scss">
.board {
    width: 100%;
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    flex-wrap: wrap;
}
</style>

