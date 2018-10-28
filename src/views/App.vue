<template>
    <div id="app">
        <el-tabs type="border-card" class="tab-component">
            <el-tab-pane class="tab-panel">
                <span slot="label">
                    <i class="el-icon-date"></i> 新番</span>
                <el-scrollbar class="tab-scroll">
                    <div class="board">
                        <anime-note :key="index" v-for="(note, index) in animeNotes" :header-text="note.text" :day="note.name" />
                    </div>
                </el-scrollbar>
            </el-tab-pane>
            <el-tab-pane class="tab-panel">
                <span slot="label">
                    <i class="el-icon-tickets"></i> 备忘</span>
                <el-scrollbar class="tab-scroll">
                    <div class="board">
                        <memo-note :key="index" v-for="(note, index) in memoNotes" :header-text="note.text" :type="note.name" />
                    </div>
                </el-scrollbar>
            </el-tab-pane>
            <el-tab-pane class="tab-panel">
                <span slot="label">
                    <i class="el-icon-setting"></i> 选项</span>
                <el-upload class="upload-button" action="" :show-file-list="false" accept="application/json" :http-request="importConfig">
                    <el-button type="primary">导入配置</el-button>
                </el-upload>
                <el-button class="export-button" @click="exportConfig">导出配置</el-button>
            </el-tab-pane>
        </el-tabs>
    </div>
</template>

<script>
import AnimeNote from './Anime/AnimeNote.vue';
import MemoNote from './Memo/MemoNote.vue';

export default {
    data: () => ({
        animeNotes: [{
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
        }],
        memoNotes: [{
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
    }),
    methods: {
        exportConfig() {
            let a = document.createElement('a');
            a.download = 'memo.json';
            a.style.display = 'none';
            document.querySelector('.export-button').parentNode.appendChild(a);
            let file = new Blob([JSON.stringify(localStorage)], { type: 'application/json' });
            a.href = URL.createObjectURL(file);
            a.click();
            a.parentNode.removeChild(a);
        },
        importConfig(e) {
            let reader = new FileReader();
            reader.addEventListener('loadend', event => {
                try {
                    let configs = JSON.parse(event.target.result);
                    for (let key in configs)
                        localStorage.setItem(key, configs[key]);
                    window.location.reload();
                }
                catch (e) { console.error(e); }
            });
            reader.readAsText(e.file);
        }
    },
    components: {
        AnimeNote,
        MemoNote
    }
};
</script>

<style lang="scss">
#app {
    height: 100%;

    .tab-component {
        height: 100%;

        .el-tabs__content {
            height: 100%;

            .tab-panel {
                height: calc(100% - 70px);

                .tab-scroll {
                    height: 100%;

                    .el-scrollbar__wrap {
                        overflow-x: hidden;

                        .board {
                            width: 100%;
                            display: flex;
                            flex-direction: row;
                            align-items: flex-start;
                            flex-wrap: wrap;
                        }
                    }
                }
                .upload-button {
                    display: inline;
                }
                .download-button {
                    text-decoration: none;
                }
            }
        }
    }
}
</style>
