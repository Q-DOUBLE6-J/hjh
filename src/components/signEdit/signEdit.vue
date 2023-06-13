<template>
    <div>
        <el-form :model="entity" label-width="120px">
            <el-form-item label="创办时间">
                <el-date-picker v-model="entity.createTime" type="datetime" placeholder="Select date and time" />
            </el-form-item>

            <el-form-item label="社团">
                <el-select v-model="entity.communityId" placeholder="请选择" @change="changeSchool">
                    <el-option v-for="item in communityArr" :key="item.id" :label="item.name" :value="item.id" />
                </el-select>
            </el-form-item>

            <el-form-item label="学生">
                <el-select v-model="entity.studentId" placeholder="请选择">
                    <el-option v-for="item in studentArr" :key="item.id" :label="item.studentName" :value="item.id" />
                </el-select>
            </el-form-item>

            <el-form-item label="状态">
                <el-select v-model="entity.checked" placeholder="请选择">
                    <el-option v-for="item in statusArr" :key="item.value" :label="item.label" :value="item.value" />
                </el-select>
            </el-form-item>

            
        </el-form>

        <div class="btnDiv">
            <el-button type="primary" @click="sure">确定</el-button>
            <el-button @click="doCancel">取消</el-button>
        </div>
    </div>
</template>
<script>
import { one, add, edit, allStudent } from "@/http/communitySignupApi/communitySignupApi"
import { allConmmunity } from "@/http/communityApi/communityApi"
import { success, fail } from "@/utils/msg/msg";
export default {
    props: { id: Number },
    mounted() {
        console.log('mounted' + this.id)
        this.get()
    },
    data() {
        return {
            entity: {
                id: 0,
                communityId: '',
                createTime: '',
                checked: '',
                studentId: 0,
            },
            statusArr: [
                { value: 0, label: '已审核' },
                { value: 1, label: '未审核' },
            ],
            communityArr: [],
            studentArr:[]
        }
    },
    methods: {
        sure() {
            this.id == 0 ? this.add() : this.edit()
        },
        add() {
            console.log('新建的:' + JSON.stringify(this.entity))
            add(this.entity).then(resp => {
                success('新建成功')
                this.doCancel()
            })
        },
        edit() {
            edit(this.entity).then(resp => {
                success('已保存')
                this.doCancel()
            })
        },
        doCancel() {
            this.$emit('doCancel')
        },
        get() {
            if (this.id != 0) {
                one({ id: this.id }).then(resp => {
                    console.log('编辑收到的:' + JSON.stringify(resp))
                    this.entity = resp.data.community
                })
            }

            allConmmunity().then(resp => {
                console.log('allConmmunity收到回复：' + JSON.stringify(resp.data))
                this.communityArr = resp.data.communities
            })

            allStudent().then(resp => {
                console.log('allStudent收到回复：' + JSON.stringify(resp.data))
                this.studentArr = resp.data.students
            })
        },
    },
}
</script>
<style  scoped>
.btnDiv {
    margin-top: 10px;
    display: flex;
    justify-content: center;
}
</style>
