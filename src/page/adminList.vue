<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <el-table
		      :data="tableData"
		      style="width: 100%">
		      <el-table-column
		        prop="loginName"
		        label="登录名"
		        width="180">
		      </el-table-column>
                <el-table-column
                    prop="trueName"
                    label="姓名"
                    width="180">
                </el-table-column>
		      <el-table-column
		        prop="createTime"
		        label="注册日期"
		        width="220">
		      </el-table-column>
              <el-table-column
                  label="是否启用"
                prop="enabled"
                width="100">
              </el-table-column>
            <el-table-column label="操作" width="200">
                <template scope="scope">
                    <el-button
                        size="mini"
                        @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button
                        size="mini"
                        type="Success"
                        @click="addFood(scope.$index, scope.row)">添加</el-button>
                    <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column>
		      <!--<el-table-column-->
		        <!--prop="admin"-->
		        <!--label="权限"-->
		      <!--</el-table-column>-->
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
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {adminList, adminCount} from '@/api/getData'
    export default {
        data(){
            return {
                tableData: [],
                currentRow: null,
                offset: 0,
                limit: 20,
                count: 0,
                currentPage: 1,
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
                try{
                    const countData = await adminCount();
                    if (countData.code == 1) {
                        this.count = countData.obj;
                    }else{
                        throw new Error('获取数据失败');
                    }
                    this.getAdmin();
                }catch(err){
                    console.log('获取数据失败', err);
                }
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.offset = (val - 1)*this.limit;
                this.getAdmin()
            },
            async getAdmin(){
                try{
                    const res = await adminList({limit: this.offset, page: this.limit});
                    if (res.code == 1) {
                    	this.tableData = [];
                    	res.obj.forEach(item => {
                            if(item.enabled == '0'){
                                item.enabled = '禁用';
                            }else{
                                item.enabled = '启用';
                            }
                            const tableItem = {
                    			createTime: item.createTime,
                                loginName: item.loginName,
                                trueName: item.trueName,
                                enabled: item.enabled,
                                //city: item.city,
                    		}
                    		this.tableData.push(tableItem)
                    	})
                    }else{
                    	throw new Error(res.message)
                    }
                }catch(err){
                    console.log('获取数据失败', err);
                }
            }
        },
    }
</script>

<style lang="less">
	@import '../style/mixin';
    .table_container{
        padding: 20px;
    }
</style>


