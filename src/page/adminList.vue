<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <!--工具条-->
            <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
                <el-form :inline="true" :model="filters">
                    <!-- <el-form-item>
                        <el-input v-model="filters.name" placeholder="姓名"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" v-on:click="getUsers">查询</el-button>
                    </el-form-item> -->
                    <el-form-item>
                        <el-button type="primary" @click="handleAdd">新增</el-button>
                    </el-form-item>
                </el-form>
            </el-col>
            <el-table :data="tableData" style="width: 100%">
                <el-table-column prop="loginName" label="登录名" ></el-table-column>
                <el-table-column prop="trueName" label="姓名" ></el-table-column>
                <el-table-column prop="createTime" label="注册日期" format></el-table-column>
                <el-table-column prop="enabled" label="是否启用"></el-table-column>
                <el-table-column label="操作" width="200">
                    <template scope="scope">
                        <el-button
                            size="mini"
                            @click="handleEdit(scope.$index, scope.row)">编辑
                        </el-button>
                       <!--  <el-button
                            size="mini"
                            type="danger"
                            @click="handleDelete(scope.$index, scope.row)">删除
                        </el-button> -->
                    </template>
                </el-table-column>
    
            </el-table>
            <div class="Pagination" style="text-align: right;margin-top: 10px;">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-size="20"
                    layout="total, prev, pager, next"
                    :total="count">
                </el-pagination>
            </div>

            <!--新增界面-->
            <el-dialog title="新增" v-model="addFormVisible" :close-on-click-modal="false">
                <el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
                    <el-form-item label="登录名称" prop="loginName">
                        <el-input v-model="addForm.loginName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="登录密码" prop="loginPwd">
                        <el-input v-model="addForm.loginPwd" auto-complete="off"></el-input>
                    </el-form-item>
                     <el-form-item label="真实姓名" prop="trueName">
                        <el-input v-model="addForm.trueName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="手机号" prop="mobile">
                        <el-input v-model="addForm.mobile" auto-complete="off"></el-input>
                    </el-form-item>
                     <el-form-item label="启用状态" prop="status">                     
                        <el-select v-model="value" placeholder="请选择">
                            <el-option
                              v-for="item in status"
                              :key="item.value"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                        </el-select>

                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click.native="addFormVisible = false">取消</el-button>
                    <el-button type="primary" @click.native="submitAdmin('addForm')" :loading="addLoading">提交</el-button>
                </div>
            </el-dialog>

            <!--编辑界面-->
            <el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false">
                <el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
                    <el-form-item label="登录名称" prop="loginName">
                        <el-input v-model="editForm.loginName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="登录密码" prop="loginPwd">
                        <el-input v-model="editForm.loginPwd" auto-complete="off"></el-input>
                    </el-form-item>
                     <el-form-item label="真实姓名" prop="trueName">
                        <el-input v-model="editForm.trueName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="手机号" prop="mobile">
                        <el-input v-model="editForm.mobile" auto-complete="off"></el-input>
                    </el-form-item>
                     <el-form-item label="启用状态" prop="status">                     
                        <el-select v-model="editValue" :placeholder="editSelect.label">
                            <el-option
                              v-for="item in status"
                              :key="item.value"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                        </el-select>

                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click.native="editFormVisible = false">取消</el-button>
                    <el-button type="primary" @click.native="editAdmin('editForm')" :loading="editLoading">提交</el-button>
                </div>
            </el-dialog>
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {adminList, adminCount,insertAdmin,updateAdmin} from '@/api/getData'
    export default {
        data(){
            return {
                filters: {
                    name: ''
                },
                tableData: [],
                currentRow: null,
                pageNum: 1,
                pageSize: 20,
                count: 0,
                currentPage: 1,
                addFormVisible: false,//新增界面是否显示
                //新增界面数据
                addForm: {
                    loginName: '',
                    loginPwd: '',
                    tureName: '',
                    mobile: '',
                    value: '1',
                },
                addLoading: false,
                addFormRules: {
                    loginName: [
                        {required: true, message: '请输入登录名称', trigger: 'blur'}
                    ],
                    loginPwd: [
                        {required: true, message: '请输入密码', trigger: 'blur'}
                    ],
                    trueName: [
                        {required: true, message: '请输入真实姓名', trigger: 'blur'}
                    ],
                    mobile: [
                        {required: true, message: '请输入手机号', trigger: 'blur'}
                    ],

                },
               
                editFormVisible: false,//编辑界面是否显示
                editLoading: false,
                editFormRules: {
                    loginName: [
                        {required: true, message: '请输入登录名称', trigger: 'blur'}
                    ],
                    loginPwd: [
                        {required: true, message: '请输入密码', trigger: 'blur'}
                    ],
                    trueName: [
                        {required: true, message: '请输入真实姓名', trigger: 'blur'}
                    ],
                    mobile: [
                        {required: true, message: '请输入手机号', trigger: 'blur'}
                    ],

                },
                editSelect :{},

                status: [{
                    value: '0',
                    label: '禁用'
                },{
                    value: '1',
                    label: '启用'
                }],
                value:'1',

                //编辑界面数据
                editForm: {
                    adminId :''
                },
                editValue : ''
            }
        },
        components: {
            headTop,
        },
        created(){
            this.initData();
        },
        methods: {
            async initData(){
                try {
                    const countData = await adminCount();
                    if (countData.code == 1) {
                        this.count = countData.obj;
                    } else {
                        throw new Error('获取数据失败');
                    }
                    this.getAdmin();
                } catch (err) {
                    console.log('获取数据失败', err);
                }
            },
            //显示新增界面
            handleAdd: function () {
                this.addFormVisible = true;
                
            },
    
            submitAdmin(addForm) {
                this.$refs[addForm].validate(async (valid) => {
                    if (valid) {
                        const params = {
                            loginName: this.addForm.loginName,
                            loginPwd: this.addForm.loginPwd,
                            trueName: this.addForm.trueName,
                            mobile: this.addForm.mobile,
                            enabled: this.value
                        }
                        try{
                            const result = await insertAdmin(params);
                            if (result.code == 1) {
                                this.initData();
                                this.addForm.loginName = '';
                                this.addForm.loginPwd = '';
                                this.addForm.trueName = '';
                                this.addForm.mobile = '';
                                this.value = '1';

                                this.addFormVisible = false;
                                this.$message({
                                    type: 'success',
                                    message: '添加成功'
                                });
                            } else {
                                 this.$message({
                                    type: 'warning',
                                    message: result.message
                                });
                            }
                        }catch(err){
                            console.log(err)
                        }
                    } else {
                        this.$notify.error({
                            title: '错误',
                            message: '请检查输入是否正确',
                            offset: 100
                        });
                        return false;
                    }
                });
            },
            //显示编辑界面
            handleEdit: function (index, row) {
                this.editFormVisible = true;
                console.log(row);
                this.adminId = row.id;
                this.editForm = Object.assign({}, row);
                 if(row.enabled==0){
                    this.statusTxt = '禁用'
                }else{
                    this.statusTxt = '启用'
                }
                this.editSelect = {label: this.statusTxt, value: row.enabled}
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                console.log(this.currentPage);
                this.pageNum = this.currentPage;
                this.getAdmin()
            },

            handleSelect(index){
                this.editValue = index;       
            },

            editAdmin(editForm) {
                this.$refs[editForm].validate(async (valid) => {
                    if (valid) {
                        const params = {
                            loginName: this.editForm.loginName,
                            loginPwd: this.editForm.loginPwd,
                            trueName: this.editForm.trueName,
                            mobile: this.editForm.mobile,
                            enabled: this.editValue,
                            id:this.adminId
                        }
                        try{
                            const result = await updateAdmin(params);
                            console.log(result);
                            if (result.code == 1) {
                                this.initData();
                                this.editFormVisible = false;
                                this.$message({
                                    type: 'success',
                                    message: '修改成功'
                                });
                            } else {
                                 this.$message({
                                    type: 'warning',
                                    message: result.message
                                });
                            }
                        }catch(err){
                            console.log(err)
                        }
                    } else {
                        this.$notify.error({
                            title: '错误',
                            message: '请检查输入是否正确',
                            offset: 100
                        });
                        return false;
                    }
                });
            },

            //删除管理员
            // async handleDelete(index, row) {
            //     try{
            //         const res = await deleteResturant(row.id);
            //         if (res.status == 1) {
            //             this.$message({
            //                 type: 'success',
            //                 message: '删除店铺成功'
            //             });
            //             this.tableData.splice(index, 1);
            //         }else{
            //             throw new Error(res.message)
            //         }
            //     }catch(err){
            //         this.$message({
            //             type: 'error',
            //             message: err.message
            //         });
            //         console.log('删除店铺失败')
            //     }
            // },

            //时间格式化
            getLocalTime(nS) { 
                let time = new Date(nS);
                let y = time.getFullYear();//年
                let m = time.getMonth() + 1;//月
                let d = time.getDate();//日
                let h = time.getHours();//时
                let mm = time.getMinutes();//分
                let s = time.getSeconds();//秒
                return y+"-"+m+"-"+d+" "+h+":"+mm+":"+s
            } ,
            async getAdmin(){
                try {
                    const res = await adminList({pageNum: this.pageNum, pageSize: this.pageSize});
                    if (res.code == 1) {
                        this.tableData = res.obj.content.filter((res)=>{
                            let resNew = res;
                            resNew.createTime = this.getLocalTime(resNew.createTime);
                            if (resNew.enabled == '0') {
                                resNew.enabled = '禁用';
                            } else {
                                resNew.enabled = '启用';
                            }                   
                            return resNew;
                        })
                    } else {
                        throw new Error(res.message)
                    }
                } catch (err) {
                    console.log('获取数据失败', err);
                }
            },

        },
    }
</script>

<style lang="less">
    @import '../style/mixin';

    .table_container {
        padding: 20px;
    }
</style>


