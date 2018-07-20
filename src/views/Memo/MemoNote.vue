<template>
    <div class="board-column">
        <div class="board-column-header memo">
            {{headerText}}
            <el-button class="add-button" icon="el-icon-plus" type="text" circle @click="dialogVisible = true"></el-button>
        </div>
        <drag-list class="board-column-content" :list="list" :options="options" :changeHandler="saveList">
            <div class="note-item" slot-scope="{ element }">
                <el-container>
                    <el-main class="note-main-container">
                        <div class="note-title">{{element.field1}}</div>
                        <div class="note-subtitle">{{element.field2}}</div>
                        <div class="note-index">
                            {{element.time}}
                        </div>
                    </el-main>
                    <el-aside class="note-button-container" width="auto">
                        <div class="note-button no-drag" @click="editNote(element)">
                            <el-icon name="edit-outline"></el-icon>
                        </div>
                    </el-aside>
                </el-container>
            </div>
            <div class="no-note no-drag" slot="blank-text">没有备忘</div>
        </drag-list>
        <el-dialog title="添加备忘" :visible.sync="dialogVisible" width="30%" :show-close="false" :close-on-click-modal="false" :close-on-press-escape="false" :append-to-body="true">
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
            this.dialogVisible = true;
            this.$nextTick(() => {
                for (let formItem in this.form)
                    this.form[formItem] = element[formItem];
            });
        }
    },
    created() {
        let list = localStorage.getItem(this.type);
        if (list) this.list.push(...JSON.parse(list));
    }
};
</script>
<style lang="scss">
@import "../Components/note";

.board-column {
    .board-column-header.memo {
        background: #409eff;
    }
    .board-column-content {
        .sortable-chosen .note-item {
            border-color: #409eff;
        }
        .note-item {
            .note-button-container {
                .note-button:hover {
                    color: #409eff;
                }
            }
        }
    }
}
</style>

