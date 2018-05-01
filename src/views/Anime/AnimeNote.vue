<template>
    <div class="board-column">
        <div :class="['board-column-header', { today: isToday }]">
            {{headerText}}
            <el-button class="add-button" icon="el-icon-plus" type="text" circle @click="dialogVisible = true"></el-button>
        </div>
        <drag-list class="board-column-content" :list="list" :options="options" :changeHandler="saveList">
            <div class="anime-item" slot-scope="{ element }">
                <el-container>
                    <el-main class="anime-main-container">
                        <div class="anime-title">{{element.title}}</div>
                        <div class="anime-subtitle">{{element.subtitle}}</div>
                        <div class="anime-index-container">
                            <div class="anime-index no-drag">
                                <div class="anime-index-left">第</div>
                                <div class="anime-index-left-arrow" @click.left="element.index -=1; saveList()">
                                    <el-icon name="caret-left"></el-icon>
                                </div>
                                <div class="anime-index-content">{{element.index}}</div>
                                <div class="anime-index-right-arrow" @click.left="element.index +=1; saveList()">
                                    <el-icon name="caret-right"></el-icon>
                                </div>
                                <div class="anime-index-right">话</div>
                            </div>
                            <el-tooltip :content="element.remark" placement="top" v-if="element.remark">
                                <el-icon class="anime-remark-icon" name="warning"></el-icon>
                            </el-tooltip>
                        </div>
                    </el-main>
                    <el-aside class="anime-button-container" width="auto">
                        <div class="anime-button no-drag" @click="editNote(element)">
                            <el-icon name="edit-outline"></el-icon>
                        </div>
                    </el-aside>
                </el-container>
            </div>
            <div class="no-anime no-drag" slot="blank-text">今天没有追番</div>
        </drag-list>
        <el-dialog title="添加番剧" :visible.sync="dialogVisible" width="30%" :show-close="false" :close-on-click-modal="false" :close-on-press-escape="false">
            <el-form ref="form" :model="form" label-width="80px" label-position="left">
                <el-form-item label="番剧名称" prop="title">
                    <el-input v-model="form.title"></el-input>
                </el-form-item>
                <el-form-item label="字幕来源" prop="subtitle">
                    <el-input v-model="form.subtitle"></el-input>
                </el-form-item>
                <el-form-item label="当前话数" prop="index">
                    <el-input-number v-model="form.index" controls-position="right"></el-input-number>
                </el-form-item>
                <el-form-item label="备注" prop="remark">
                    <el-input v-model="form.remark"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button type="danger" @click="dialogClose('delete')" v-if="editing">删 除</el-button>
                <el-button @click="dialogClose('cancel')">取 消</el-button>
                <el-button type="primary" @click="dialogClose('confirm')">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>
<script>
import dragList from '../Components/dragList.vue';

export default {
    name: 'animeNote',
    data: () => ({
        list: [],
        options: {
            group: 'anime',
            animation: 100,
            filter: '.no-drag'
        },
        form: {
            title: '',
            subtitle: '',
            index: 1,
            id: 0,
            remark: ''
        },
        editing: false,
        dialogVisible: false
    }),
    components: {
        dragList
    },
    props: {
        headerText: {
            type: String,
            default: 'Header'
        },
        day: String
    },
    methods: {
        saveList() {
            localStorage.setItem(this.day, JSON.stringify(this.list));
        },
        dialogClose(type) {
            this.dialogVisible = false;
            if (type == 'confirm') {
                if (this.editing) {
                    let note = this.list[this.list.findIndex(n => n.id == this.form.id)];
                    for (let noteItem in this.form)
                        note[noteItem] = this.form[noteItem];
                }
                else {
                    this.form.id = Date.now();
                    this.list.push({ ...this.form });
                }
            } else if (type == 'delete') {
                this.list.splice(this.list.findIndex(n => n.id == this.form.id), 1);
            }
            this.$refs['form'].resetFields();
            this.editing = false;
            this.saveList();
        },
        editNote(element) {
            this.editing = true;
            for (let formItem in this.form)
                this.form[formItem] = element[formItem];
            this.dialogVisible = true;
        }
    },
    computed: {
        isToday() {
            return new Date().toDateString().indexOf(this.day) > -1;
        }
    },
    created() {
        let list = localStorage.getItem(this.day);
        if (list) this.list.push(...JSON.parse(list));
    }
};
</script>
<style lang="scss">
.board-column {
    margin: 10px;
    width: 300px;
    min-height: 100px;
    height: auto;
    overflow: hidden;
    background: #ebeef5;
    border-radius: 3px;

    .board-column-header {
        font-size: 1.2em;
        font-weight: bold;
        width: calc(100% - 40px);
        height: 50px;
        padding: 0 20px;
        background: #c0c4cc;
        color: #fff;
        border-radius: 3px 3px 0 0;
        display: inline-flex;
        justify-content: space-between;
        align-items: center;

        &.today {
            background: #67c23a;
        }
        .add-button {
            color: #fff;
            border: 1 solid transparent;
            padding: 4px;

            &:hover {
                border-color: #fff;
            }
        }
    }
    .board-column-content {
        height: auto;
        overflow: hidden;
        padding: 5px 10px;
        min-height: 60px;
        display: flex;
        justify-content: center;
        flex-direction: column;
        align-items: center;

        .sortable-chosen .anime-item {
            border-color: #67c23a;
        }
        .anime-item {
            cursor: pointer;
            background-color: #fff;
            text-align: left;
            padding: 5px 10px;
            box-sizing: border-box;
            min-height: 51px;
            border: 3px solid transparent;

            .anime-title {
                color: #222;
                overflow: hidden;
                text-overflow: ellipsis;
            }
            .anime-subtitle {
                color: #aaa;
                font-size: 0.8em;
                overflow: hidden;
                text-overflow: ellipsis;
            }
            .anime-index-container {
                display: flex;
                align-items: center;
                font-size: 0.8em;

                .anime-index {
                    color: #fb7299;
                    display: flex;
                    align-items: center;

                    .anime-index-content {
                        margin: 0 5px;
                    }
                    .anime-index-left,
                    .anime-index-right {
                        display: unset;
                    }
                    .anime-index-left-arrow,
                    .anime-index-right-arrow {
                        display: none;
                        border-radius: 3px;
                        color: #aaa;
                        transition: color 0.15s;

                        &:hover {
                            color: #fb7299;
                        }
                    }
                    &:hover {
                        .anime-index-left,
                        .anime-index-right {
                            display: none;
                        }
                        .anime-index-left-arrow,
                        .anime-index-right-arrow {
                            display: unset;
                        }
                    }
                }
                .anime-remark-icon {
                    color: #409eff;
                    margin-left: 5px;
                }
            }
            .anime-main-container {
                padding: unset;
                overflow: hidden;
            }
            .anime-button-container {
                display: flex;
                align-items: center;

                .anime-button {
                    color: #aaa;
                    font-size: 1.6em;
                    display: none;
                    transition: color 0.15s;

                    &:hover {
                        color: #67c23a;
                    }
                }
            }
            &:hover {
                .anime-button {
                    display: unset;
                }
            }
        }
        .no-anime {
            color: #909399;
            font-size: 0.9em;
            text-align: center;
        }
    }
}
</style>

