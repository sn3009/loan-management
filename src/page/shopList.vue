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
                <el-table-column type="expand">
                    <template scope="props">
                        <el-form label-position="left" inline class="demo-table-expand">
                            <el-form-item label="id">
                                <span>{{ props.row.id }}</span>
                            </el-form-item>
                            <el-form-item label="合作商名称">
                                <span>{{ props.row.appName }}</span>
                            </el-form-item>
                            <el-form-item label="logo">
                                <span>{{ props.row.logo }}</span>
                            </el-form-item>
                            <el-form-item label="费率">
                                <span>{{ props.row.rate }}</span>
                            </el-form-item>
                            <el-form-item label="服务费">
                                <span>{{ props.row.serviceCharge }}</span>
                            </el-form-item>
                            <el-form-item label="最高额度">
                                <span>{{ props.row.topQota }}</span>
                            </el-form-item>
                            <el-form-item label="最低额度">
                                <span>{{ props.row.bottomQota }}</span>
                            </el-form-item>
                            <el-form-item label="放款时间(小时)" style="white-space: nowrap;">
                                <span>{{ props.row.outTime }}</span>
                            </el-form-item>
                            <el-form-item label="是否可用">
                                <span>{{ props.row.statusTxt }}</span>
                            </el-form-item>
                        </el-form>
                    </template>
                </el-table-column>
                <el-table-column label="合作商名称" prop="appName"></el-table-column>
                <el-table-column label="服务费" prop="serviceCharge"></el-table-column>
                <el-table-column label="费率" prop="rate"></el-table-column>
                <el-table-column label="是否可用" prop="statusTxt"></el-table-column>

                <el-table-column label="操作" width="200">
                    <template scope="scope">
                        <el-button
                            size="mini"
                            @click="handleEdit(scope.$index, scope.row)">编辑
                        </el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="Pagination">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-size="20"
                    layout="total, prev, pager, next"
                    :total="count">
                </el-pagination>
            </div>
            <!--新增-->
            <el-dialog title="新增" v-model="addFormVisible">
                <el-form :model="addForm" :rules="addFormRules" ref="addForm">
                    <el-form-item label="合作商名称" label-width="120px" prop='appName'>
                        <el-input v-model="addForm.appName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="logo" label-width="120px" prop='logo'>
                        <el-input v-model="addForm.logo" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="费率" label-width="120px" prop='rate'>
                        <el-input v-model.number="addForm.rate"></el-input>
                    </el-form-item>
                    <el-form-item label="服务费" label-width="120px" prop='serviceCharge'>
                        <el-input v-model.number="addForm.serviceCharge"></el-input>
                    </el-form-item>
                    <el-form-item label="最高额度" label-width="120px" prop='topQota'>
                        <el-input v-model.number="addForm.topQota"></el-input>
                    </el-form-item>
                    <el-form-item label="最低额度" label-width="120px" prop='bottomQota'>
                        <el-input v-model.number="addForm.bottomQota"></el-input>
                    </el-form-item>
                    <el-form-item label="放款时间(小时)" label-width="120px" prop='outTime'>
                        <el-input v-model.number="addForm.outTime"></el-input>
                    </el-form-item>
                     <el-form-item label="启用状态" label-width="120px">
                        <el-select v-model="selectStatus"  @change="handleSelect" :placeholder="selectMenu.label">
                            <el-option
                              v-for="item in status" 
                              :key="item.id"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                        </el-select>
                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="submitLoan('addForm')">确 定</el-button>
                </div>
            </el-dialog>
            <!--修改-->
            <el-dialog title="修改" v-model="dialogFormVisible">
                <el-form :model="selectTable" :rules="editloanRules" ref="editloan">
                    <el-form-item label="合作商名称" label-width="120px" prop='appName'>
                        <el-input v-model="selectTable.appName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="logo" label-width="120px" prop='logo'>
                        <el-input v-model="selectTable.logo" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="费率" label-width="120px" prop='rate'>
                        <el-input v-model.number="selectTable.rate"></el-input>
                    </el-form-item>
                    <el-form-item label="服务费" label-width="120px" prop='serviceCharge'>
                        <el-input v-model.number="selectTable.serviceCharge"></el-input>
                    </el-form-item>
                    <el-form-item label="最高额度" label-width="120px" prop='topQota'>
                        <el-input v-model.number="selectTable.topQota"></el-input>
                    </el-form-item>
                    <el-form-item label="最低额度" label-width="120px" prop='bottomQota'>
                        <el-input v-model.number="selectTable.bottomQota"></el-input>
                    </el-form-item>
                    <el-form-item label="放款时间(小时)" label-width="120px" prop='outTime'>
                        <el-input v-model.number="selectTable.outTime"></el-input>
                    </el-form-item>
                     <el-form-item label="启用状态" label-width="120px">
                        <el-select v-model="selectStatus"  @change="handleSelect" :placeholder="selectMenu.label">
                            <el-option
                              v-for="item in status" 
                              :key="item.id"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                        </el-select>
                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="editHotLoan">确 定</el-button>
                </div>
            </el-dialog>
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {baseUrl, baseImgPath} from '@/config/env'
    import {
        getCooperation,
        updateHotLoan,
        insertHotLoan,
    } from '@/api/getData'
    export default {
        data(){
            return {
                baseUrl,
                baseImgPath,
                filters: {
                    name: ''
                },
                city: {},
                pageNum: 1,
                pageSize: 20,
                count: 0,
                tableData: [],
                currentPage: 1,
                addForm:{},
                selectTable: {},
                addFormVisible: false,//新增界面是否显示
                addFormRules: {
                    appName: [
                        {required: true, message: '请输入合作商名称', trigger: 'blur'}
                    ],
                    logo: [
                        {required: true, message: '请输入logo图片', trigger: 'blur'}
                    ],
                    rate: [
                        {required: true, message: '请输入费率'},
                        {type: 'number',message: '费率必须是数字'}

                    ],
                    serviceCharge: [
                        {required: true, message: '请输入服务费'},
                        {type: 'number',message: '服务费必须是数字'}

                    ],
                    topQota: [
                        {required: true, message: '请输入最高额度'},
                        {type: 'number',message: '最高额度必须是数字'}


                    ],
                    bottomQota: [
                        {required: true, message: '请输入最低额度'},
                        {type: 'number',message: '最低额度必须是数字'}

                    ],
                    outTime: [
                        {required: true, message: '请输入放款时间'},
                        {type: 'number',message: '放款时间必须是数字'}
                    ],

                },

                dialogFormVisible: false,//编辑页面是否显示
                //编辑
                loanId: '',
                editloanRules: {
                   appName: [
                        {required: true, message: '请输入合作商名称', trigger: 'blur'}
                    ],
                    logo: [
                        {required: true, message: '请输入logo图片', trigger: 'blur'}
                    ],
                    rate: [
                        {required: true, message: '请输入费率'},
                        {type: 'number',message: '费率必须是数字'}

                    ],
                    serviceCharge: [
                        {required: true, message: '请输入服务费'},
                        {type: 'number',message: '服务费必须是数字'}

                    ],
                    topQota: [
                        {required: true, message: '请输入最高额度'},
                        {type: 'number',message: '最高额度必须是数字'}


                    ],
                    bottomQota: [
                        {required: true, message: '请输入最低额度'},
                        {type: 'number',message: '最低额度必须是数字'}

                    ],
                    outTime: [
                        {required: true, message: '请输入放款时间'},
                        {type: 'number',message: '放款时间必须是数字'}
                    ],

                },
              
                status: [{
                    value: '0',
                    label: '禁用'
                },{
                    value: '1',
                    label: '启用'
                }],
                selectStatus :null,
                selectMenu :{},
                statusTxt : '',
            }
        },
        created(){
            this.initData();
        },
        components: {
            headTop,
        },
        methods: {
            async initData(){
                try {
                    this.getCooperation();
                } catch (err) {
                    console.log('获取数据失败', err);
                }
            },
            async getCategory(){
                this.status = [{
                    value: '1',
                    label: '启用'
                },{
                    value: '0',
                    label: '禁用'
                }]
            
            },
             //显示新增界面
            handleAdd: function () {
                this.addFormVisible = true;
                
            },
            submitLoan(addForm) {
                this.$refs[addForm].validate(async (valid) => {
                    if (valid) {
                        const params = {
                            appName: this.addForm.appName,
                            logo: this.addForm.logo,
                            rate: this.addForm.rate,
                            serviceCharge: this.addForm.serviceCharge,
                            topQota: this.addForm.topQota,
                            bottomQota: this.addForm.bottomQota,
                            outTime: this.addForm.outTime,
                            enabled: this.selectStatus
                        }
                        try{
                            const result = await insertHotLoan(params);
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
            async getCooperation(){
                const cooperations = await getCooperation({type: 0, pageNum: this.pageNum, pageSize: this.pageSize});
                this.tableData = [];
                this.count = cooperations.obj.totalElements;
                cooperations.obj.content.forEach(item => {
                    const tableData = {};
                    tableData.appName = item.appName;
                    tableData.logo = item.logo;
                    tableData.id = item.id;
                    tableData.rate = item.rate;
                    tableData.serviceCharge = item.serviceCharge;
                    tableData.topQota = item.topQota;
                    tableData.bottomQota = item.bottomQota;
                    tableData.outTime = item.outTime;
                    tableData.enabled = item.enabled;
                    if(item.enabled==0){
                        tableData.statusTxt = '禁用'
                    }else{
                        tableData.statusTxt = '启用'
                    }
                    tableData.createTimeString = item.createTimeString;
                    this.tableData.push(tableData);
                })
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.pageNum = this.currentPage;
                this.getCooperation();
            },
            handleEdit(index, row) {
                console.log(row);
                this.selectTable = row;
                this.dialogFormVisible = true;
                this.loanId = row.id;

                if(row.enabled==0){
                    this.statusTxt = '禁用'
                }else{
                    this.statusTxt = '启用'

                }
               
                this.selectMenu = {label: this.statusTxt, value: row.enabled}
               
            },
            handleSelect(index){
        
                this.selectStatus = index;

            },
            async editHotLoan(){
                this.dialogFormVisible = false;
                try {
                    Object.assign(this.selectTable, this.address);
                    this.selectTable.id = this.loanId;
                    this.selectTable.enabled = this.selectStatus;

                    console.log(this.selectStatus);
                    console.log(this.loanId);
                    console.log(this.selectTable);
                    const res = await updateHotLoan(this.selectTable)
                    if (res.code == 1) {
                        this.$message({
                            type: 'success',
                            message: '更新合作商信息成功'
                        });
                        this.getCooperation();
                    } else {
                        this.$message({
                            type: 'error',
                            message: res.message
                        });
                    }
                } catch (err) {
                    console.log('更新合作商信息失败', err);
                }
            },
        },
    }
</script>

<style lang="less">
    @import '../style/mixin';

    .demo-table-expand {
        font-size: 0;
    }

    .demo-table-expand label {
        width: 100px;
        color: #99a9bf;
    }

    .demo-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }

    .table_container {
        padding: 20px;
    }

    .Pagination {
        display: flex;
        justify-content: flex-end;
        margin-top: 8px;
    }

    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }

    .avatar-uploader .el-upload:hover {
        border-color: #20a0ff;
    }

    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 120px;
        height: 120px;
        line-height: 120px;
        text-align: center;
    }

    .avatar {
        width: 120px;
        height: 120px;
        display: block;
    }
</style>
