/*
 * Copyright 2014-2019 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
<template>
    <div>
        <el-dialog :title="$t('contracts.createFolder')" :visible.sync="dialogVisible" :before-close="modelClose" class="dialog-wrapper" width="433px" :center="true">
            <div>
                <el-form :model="folderFrom" :rules="rules" ref="folderFrom" label-width="110px" class="demo-ruleForm">
                    <el-form-item :label="$t('contracts.folderName')" prop="folderName" style="width:330px">
                        <el-input v-model="folderFrom.folderName" @keyup.enter.native="handleFolder"></el-input>
                    </el-form-item>
                </el-form>
            </div>
            <div class="text-right sure-btn" style="margin-top:10px">
                <el-button @click="modelClose">{{this.$t("text.cancel")}}</el-button>
                <el-button type="primary" @click="submit('folderFrom')">{{this.$t("text.sure")}}</el-button>
            </div>
        </el-dialog>
    </div>
</template>
<script>
export default {
    name: "addFolder",
    props: ['foldershow'],
    computed: {
        rules() {
            let data = {
                folderName: [
                    {
                        required: true,
                        message: this.$t("rule.folderName"),
                        trigger: "blur"
                    },
                    {
                        min: 1,
                        max: 12,
                        message: this.$t("rule.folderLong"),
                        trigger: "blur"
                    },
                    {
                        pattern: /^[A-Za-z0-9_]+$/,
                        message: this.$t("rule.folderRule"),
                        trigger: "blur"
                    }
                ]
            }
            return data
        }
    },
    data: function(){
        return {
            folderFrom: {
                folderName: ""
            },
            dialogVisible: this.foldershow,
            folderList: []
        }
    },
    mounted: function(){
        if(localStorage.getItem("folderList")){
            this.folderList = JSON.parse(localStorage.getItem("folderList")) 
        }
    },
    methods: {
        submit: function(formName){
            this.$refs[formName].validate(valid => {
                if (valid) {
                    let num = 0
                    this.folderList.forEach(value => {
                        if(value.folderName == this.folderFrom.folderName && value.groupId == localStorage.getItem("groupId")){
                            num ++
                            this.$message({
                                message: this.$t("contracts.folderSameFail"),
                                type: "error"
                            });
                        }
                    })
                    if(num == 0){
                        let data = {
                                folderName: this.folderFrom.folderName,
                                folderId: (new Date()).getTime(),
                                folderActive: false,
                                groupId: localStorage.getItem("groupId")
                            }
                        this.folderList.push(data)
                        localStorage.setItem('folderList',JSON.stringify(this.folderList))
                        this.$emit("success")
                    }
                }else{
                    return false;
                }
            })
        },
        modelClose: function(){
            this.$emit("close")
        },
        handleFolder() {
            console.log('enter')
        }
    }
}
</script>

