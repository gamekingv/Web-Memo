<template>
    <div id="app">
        <el-tabs type="border-card" class="tab-component">
            <el-tab-pane class="tab-panel">
                <span slot="label">
                    <i class="el-icon-date"></i> 新番</span>
                <el-scrollbar class="tab-scroll">
                    <anime></anime>
                </el-scrollbar>
            </el-tab-pane>
            <el-tab-pane class="tab-panel">
                <span slot="label">
                    <i class="el-icon-tickets"></i> 备忘</span>
                <el-scrollbar class="tab-scroll">
                    <memo></memo>
                </el-scrollbar>
            </el-tab-pane>
            <el-tab-pane class="tab-panel">
                <span slot="label">
                    <i class="el-icon-setting"></i> 选项</span>
                <el-upload class="upload-button" action="" :show-file-list="false" accept="application/json" :http-request="importConfig">
                    <el-button type="primary">导入</el-button>
                </el-upload>
                <el-button class="export-button" @click="exportConfig">生成配置</el-button>
            </el-tab-pane>
        </el-tabs>
    </div>
</template>

<script>
import Anime from './Anime/Anime.vue';
import Memo from './Memo/Memo.vue';

export default {
    methods: {
        exportConfig() {
            let a = document.querySelector('.download-button');
            if (!a) {
                a = document.createElement('a');
                a.download = 'memo.json';
                a.innerHTML = '下载配置';
                a.className = 'el-button download-button';
                document.querySelector('.export-button').parentNode.appendChild(a);
            }
            let file = new Blob([JSON.stringify(localStorage)], { type: 'application/json' });
            a.href = URL.createObjectURL(file);
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
        Anime,
        Memo
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
