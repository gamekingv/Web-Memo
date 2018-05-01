<template>
    <div class="board">
        <memo-note :key="index" v-for="(note, index) in notes" :header-text="note.text" :type="note.name" />
    </div>
</template>
<script>
import MemoNote from './MemoNote.vue';

export default {
    name: 'anime',
    components: {
        MemoNote
    },
    data: () => ({
        notes: [{
            text: '歌曲',
            name: 'Song'
        }, {
            text: '影视',
            name: 'Film'
        }, {
            text: '动漫',
            name: 'Anime'
        }, {
            text: '游戏',
            name: 'Game'
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

