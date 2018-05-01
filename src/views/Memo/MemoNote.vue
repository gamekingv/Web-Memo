<template>
    <div class="board-column">
        <div class="board-column-header memo">
            {{headerText}}
            <el-button class="add-button" icon="el-icon-plus" type="text" circle @click="dialogVisible = true"></el-button>
        </div>
        <drag-list class="board-column-content" :list="list" :options="options" :changeHandler="saveList">
            <div class="memo-item" slot-scope="{ element }">
                <el-container>
                    <el-main class="memo-main-container">
                        <div class="memo-title">{{element.field1}}</div>
                        <div class="memo-subtitle">{{element.field2}}</div>
                        <div class="memo-index">
                            {{element.time}}
                        </div>
                    </el-main>
                    <el-aside class="memo-button-container" width="auto">
                        <div class="memo-button no-drag" @click="editNote(element)">
                            <el-icon name="edit-outline"></el-icon>
                        </div>
                    </el-aside>
                </el-container>
            </div>
            <div class="no-memo no-drag" slot="blank-text">没有备忘</div>
        </drag-list>
        <el-dialog title="添加备忘" :visible.sync="dialogVisible" width="30%" :show-close="false" :close-on-click-modal="false" :close-on-press-escape="false">
            <el-form ref="form" :model="form" label-width="80px" label-position="left">
                <el-form-item label="字段1" prop="field1">
                    <el-input v-model="form.field1"></el-input>
                </el-form-item>
                <el-form-item label="字段2" prop="field2">
                    <el-input v-model="form.field2"></el-input>
                </el-form-item>
                <el-form-item label="时间" prop="time">
                    <el-date-picker v-model="form.time" type="date" placeholder="选择日期" value-format="yyyy/MM/dd">
                    </el-date-picker>
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
    name: 'memoNote',
    data: () => ({
        list: [],
        options: {
            group: 'memo',
            animation: 100,
            filter: '.no-drag'
        },
        form: {
            field1: '',
            field2: '',
            time: '',
            id: 0
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
        type: String
    },
    methods: {
        saveList() {
            localStorage.setItem(this.type, JSON.stringify(this.list));
        },
        dialogClose(type) {
            this.dialogVisible = false;
            if (type == 'confirm') {
                if (this.editing) {
                    let note = this.list[this.list.findIndex(n => n.id == this.form.id)];
                    for (let noteItem in note)
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
    created() {
        let list = localStorage.getItem(this.type);
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

        &.memo {
            background: #409eff;
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

        .sortable-chosen .memo-item {
            border-color: #409eff;
        }
        .memo-item {
            cursor: pointer;
            background-color: #fff;
            text-align: left;
            padding: 5px 10px;
            box-sizing: border-box;
            min-height: 51px;
            border: 3px solid transparent;

            .memo-title {
                color: #222;
                overflow: hidden;
                text-overflow: ellipsis;
            }
            .memo-subtitle {
                color: #aaa;
                font-size: 0.8em;
                overflow: hidden;
                text-overflow: ellipsis;
            }
            .memo-index {
                color: #fb7299;
                font-size: 0.8em;
                display: flex;

                .memo-index-content {
                    margin: 0 5px;
                }
                .memo-index-left,
                .memo-index-right {
                    display: unset;
                }
                .memo-index-left-arrow,
                .memo-index-right-arrow {
                    display: none;
                    border-radius: 3px;
                    color: #aaa;
                    transition: color 0.15s;

                    &:hover {
                        color: #fb7299;
                    }
                }
                &:hover {
                    .memo-index-left,
                    .memo-index-right {
                        display: none;
                    }
                    .memo-index-left-arrow,
                    .memo-index-right-arrow {
                        display: unset;
                    }
                }
            }
            .memo-main-container {
                padding: unset;
                overflow: hidden;
            }
            .memo-button-container {
                display: flex;
                align-items: center;

                .memo-button {
                    color: #aaa;
                    font-size: 1.6em;
                    display: none;
                    transition: color 0.15s;

                    &:hover {
                        color: #409eff;
                    }
                }
            }
            &:hover {
                .memo-button {
                    display: unset;
                }
            }
        }
        .no-memo {
            color: #909399;
            font-size: 0.9em;
            text-align: center;
        }
    }
}
</style>

