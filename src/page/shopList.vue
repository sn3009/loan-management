<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
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
                                <span>{{ props.row.enabled }}</span>
                            </el-form-item>
                        </el-form>
                    </template>
                </el-table-column>
                <el-table-column label="合作商名称" prop="appName"></el-table-column>
                <el-table-column label="logo" prop="logo"></el-table-column>
                <el-table-column label="费率" prop="rate"></el-table-column>
                <el-table-column label="操作" width="200">
                    <template scope="scope">
                        <el-button
                            size="mini"
                            @click="handleEdit(scope.$index, scope.row)">编辑
                        </el-button>
                        <el-button
                            size="mini"
                            type="Success"
                            @click="addFood(scope.$index, scope.row)">添加
                        </el-button>
                        <el-button
                            size="mini"
                            type="danger"
                            @click="handleDelete(scope.$index, scope.row)">删除
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
            <!--修改-->
            <el-dialog title="修改" v-model="dialogFormVisible">
                <el-form :model="selectTable">
                    <el-form-item label="合作商名称" label-width="100px">
                        <el-input v-model="selectTable.appName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="logo" label-width="100px">
                        <el-input v-model="selectTable.logo" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="费率" label-width="100px">
                        <el-input v-model="selectTable.rate"></el-input>
                    </el-form-item>
                    <el-form-item label="服务费" label-width="100px">
                        <el-input v-model="selectTable.serviceCharge"></el-input>
                    </el-form-item>
                    <el-form-item label="最高额度" label-width="100px">
                        <el-input v-model="selectTable.topQota"></el-input>
                    </el-form-item>
                    <el-form-item label="最低额度" label-width="100px">
                        <el-input v-model="selectTable.bottomQota"></el-input>
                    </el-form-item>
                    <el-form-item label="放款时间(小时)" label-width="100px">
                        <el-input v-model="selectTable.outTime"></el-input>
                    </el-form-item>
                    <!-- <el-form-item label="是否可用" label-width="100px">
                        <el-cascader :options="categoryOptions" v-model="selectedCategory" change-on-select></el-cascader>
                    </el-form-item> -->
                     <el-form-item label="启用状态" label-width="100px">
                        <el-select v-model="selectStatus"  @change="handleSelect" :placeholder="selectMenu.label">
                            <el-option
                              v-for="item in status" 
                              :key="item.id"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                        </el-select>
                    </el-form-item>
                  
                    <!--<el-form-item label="商铺图片" label-width="100px">-->
                        <!--<el-upload-->
                            <!--class="avatar-uploader"-->
                            <!--:action="baseUrl + '/v1/addimg/shop'"-->
                            <!--:show-file-list="false"-->
                            <!--:on-success="handleServiceAvatarScucess"-->
                            <!--:before-upload="beforeAvatarUpload">-->
                            <!--<img v-if="selectTable.image_path" :src="baseImgPath + selectTable.image_path"-->
                                 <!--class="avatar">-->
                            <!--<i v-else class="el-icon-plus avatar-uploader-icon"></i>-->
                        <!--</el-upload>-->
                    <!--</el-form-item>-->
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="updateShop">确 定</el-button>
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
        getCooperationCount,
        // foodCategory,
        // updateResturant,
        // searchplace,
        // deleteResturant
    } from '@/api/getData'
    export default {
        data(){
            return {
                baseUrl,
                baseImgPath,
                city: {},
                offset: 0,
                limit: 20,
                count: 0,
                tableData: [],
                currentPage: 1,
                selectTable: {},
                dialogFormVisible: false,
                categoryOptions: [],
                selectedCategory: [],
                address: {},
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
                    //this.city = await cityGuess();
                    const countData = await getCooperationCount();
                    if (countData.code == 1) {

                        this.count = countData.obj;
                    } else {
                        throw new Error('获取数据失败');
                    }
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
            async getCooperation(){
                //const {latitude, longitude} = this.city;
                const cooperations = await getCooperation({type: 1, limit: this.offset, page: this.limit});
                this.tableData = [];
                console.log(cooperations);
                cooperations.obj.forEach(item => {
                    const tableData = {};
                    tableData.appName = item.appName;
                    tableData.logo = item.logo;
                    tableData.rate = item.rate;
                    tableData.serviceCharge = item.serviceCharge;
                    tableData.topQota = item.topQota;
                    tableData.bottomQota = item.bottomQota;
                    tableData.outTime = item.outTime;
                    tableData.enabled = item.enabled;
                    tableData.createTimeString = item.createTimeString;
                    this.tableData.push(tableData);
                })
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                console.log(val);
                this.offset = (val - 1) * this.limit;
                console.log(this.offset);
                this.getCooperation()
            },
            handleEdit(index, row) {
                console.log(row);
                this.selectTable = row;
                this.dialogFormVisible = true;
                // this.selectStatus = row.enabled;
                if(row.enabled==0){
                    this.statusTxt = '禁用'
                }else{
                    this.statusTxt = '启用'

                }
                console.log(this.statusTxt)
                this.selectMenu = {label: this.statusTxt, value: row.enabled}
                console.log(this.selectMenu.label)
                if (!this.categoryOptions.length) {
                    console.log(11);
                    this.getCategory();
                }
            },
            handleSelect(index){
                console.log(index);
                this.selectStatus = index;
            

                console.log(this.selectMenu)
            },
            addFood(index, row){
                this.$router.push({path: 'addGoods', query: {restaurant_id: row.id}})
            },
            async handleDelete(index, row) {
                try {
                    const res = await deleteResturant(row.id);
                    if (res.status == 1) {
                        this.$message({
                            type: 'success',
                            message: '删除店铺成功'
                        });
                        this.tableData.splice(index, 1);
                    } else {
                        throw new Error(res.message)
                    }
                } catch (err) {
                    this.$message({
                        type: 'error',
                        message: err.message
                    });
                    console.log('删除店铺失败')
                }
            },
            // async querySearchAsync(queryString, cb) {
            //     if (queryString) {
            //         try {
            //             const cityList = await searchplace(this.city.id, queryString);
            //             if (cityList instanceof Array) {
            //                 cityList.map(item => {
            //                     item.value = item.address;
            //                     return item;
            //                 })
            //                 cb(cityList)
            //             }
            //         } catch (err) {
            //             console.log(err)
            //         }
            //     }
            // },
            // addressSelect(vale){
            //     const {address, latitude, longitude} = vale;
            //     this.address = {address, latitude, longitude};
            // },
            // handleServiceAvatarScucess(res, file) {
            //     if (res.status == 1) {
            //         this.selectTable.image_path = res.image_path;
            //     } else {
            //         this.$message.error('上传图片失败！');
            //     }
            // },
            // beforeAvatarUpload(file) {
            //     const isRightType = (file.type === 'image/jpeg') || (file.type === 'image/png');
            //     const isLt2M = file.size / 1024 / 1024 < 2;

            //     if (!isRightType) {
            //         this.$message.error('上传头像图片只能是 JPG 格式!');
            //     }
            //     if (!isLt2M) {
            //         this.$message.error('上传头像图片大小不能超过 2MB!');
            //     }
            //     return isRightType && isLt2M;
            // },
            async updateShop(){
                this.dialogFormVisible = false;
                try {
                    Object.assign(this.selectTable, this.address);
                    this.selectTable.category = this.selectedCategory.join('/');
                    const res = await updateResturant(this.selectTable)
                    if (res.status == 1) {
                        this.$message({
                            type: 'success',
                            message: '更新店铺信息成功'
                        });
                        this.getResturants();
                    } else {
                        this.$message({
                            type: 'error',
                            message: res.message
                        });
                    }
                } catch (err) {
                    console.log('更新餐馆信息失败', err);
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
