<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i> 用户列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
                <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="search">搜索</el-button>
            </div>
            <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column prop="email" label="邮箱" width="150">
                </el-table-column>
                <el-table-column prop="mobile" label="手机号码" width="150">
                </el-table-column>
                <el-table-column prop="blockAddress" label="区块地址" width="300">
                </el-table-column>
                <el-table-column label="操作" width="180">
                    <template slot-scope="scope">
                        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">查看</el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="total">
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="" :visible.sync="editVisible" width="30%">
            <el-card class="box-card">
              <div slot="header" class="clearfix">
                <span>用户详情</span>
              </div>
              <div class="text item" style="overflow-x:scroll;">
                <p><span>用户ID</span> : <span>{{pageList.id}}</span></p>
                <p><span>区块地址</span> : <span>{{pageList.blockAddress}}</span></p>
                <p><span>邮箱</span> : <span>{{pageList.email}}</span></p>
                <p><span>手机号码</span> : <span>{{pageList.mobile}}</span></p>
                <p><span>昵称</span> : <span>{{pageList.nickname}}</span></p>
                <p><span>头像</span> : <span>{{pageList.picture}}</span></p>
                <p><span>状态</span> : <span>{{pageList.status}}</span></p>
                <p><span>是否认证</span> : <span>{{pageList.realAuthStatus}}</span></p>
                <p><span>余额</span> : <span>{{pageList.balance}}</span></p>
                <p><span>创建时间</span> : <span>{{pageList.createdAt}}</span></p>
                <p><span>更新时间</span> : <span>{{pageList.updatedAt}}</span></p>
              </div>
            </el-card>
            <!-- <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span> -->
        </el-dialog>

        <!-- 删除提示框 -->
        <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false">取 消</el-button>
                <el-button type="primary" @click="deleteRow">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                token:'',
                tableData: [],
                cur_page: 1,
                total:1,
                pageList:[],
                multipleSelection: [],
                del_list: [],
                select_word:'',
                is_search: false,
                editVisible: false,
                delVisible: false,
                idx: -1
            }
        },
        created() {
            this.token=localStorage.getItem("token");
            console.log(this.token);
            this.getData();
        },
        computed: {
            // data() {
            //     return this.tableData.filter((d) => {
            //         let is_del = false;
            //         console.log(d);
            //         for (let i = 0; i < this.del_list.length; i++) {
            //             if (d.name === this.del_list[i].name) {
            //                 is_del = true;
            //                 break;
            //             }
            //         }
            //         if (!is_del) {
            //             // if (d.email.indexOf(this.select_cate) > -1 &&
            //             //     (d.name.indexOf(this.select_word) > -1 ||
            //             //         d.email.indexOf(this.select_word) > -1)
            //             // ) {
            //             //     return d;
            //             // }
            //             return d;
            //         }
            //     })
            // }
        },
        methods: {
            // 分页导航
            handleCurrentChange(val) {
                this.cur_page = val;
                this.getData();
            },
            // 获取数据
            getData() {
                this.$axios({
                    headers: {'access_token':this.token},
                    method: 'post',
                    url: 'Member/index',
                    data: {
                        page: this.cur_page
                    }
                }).then(res=>{
                  console.log(res);
                  this.tableData = res.data.data;
                  this.total=res.data.total;
                  console.log(this.tableData)
                })
                .catch(err=>{
                  console.log(err)
                })
            },
            // search() {
            //     this.is_search = true;
            // },
            // formatter(row, column) {
            //     return row.address;
            // },
            // filterTag(value, row) {
            //     return row.tag === value;
            // },
            //查看按钮
            handleEdit(index, row) {
                this.idx = index;
                const item = this.tableData[index];
                this.editVisible = true;
                this.$axios({
                    headers: {'access_token':this.token},
                    method: 'post',
                    url: 'Member/info',
                    data: {
                        id: this.tableData[this.idx].id
                    }
                }).then(res=>{
                  console.log(res);
                  this.pageList=res.data.data
                })
            },
            //删除按钮
            handleDelete(index, row) {
                this.idx = index;
                this.delVisible = true;
            },
            //批量删除
            delAll() {
                const length = this.multipleSelection.length;
                let str = '';
                this.del_list = this.del_list.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    this.delUserBtn(this.multipleSelection[i].id);
                    str += this.multipleSelection[i].email + ' ';
                }
                this.$message.error('删除了' + str);
                this.multipleSelection = [];
                this.getData();
            },
            //批量选中
            handleSelectionChange(val) {
                this.multipleSelection = val;
                console.log(val);
            },
            // 保存编辑
            saveEdit() {
                this.$set(this.tableData, this.idx, this.form);
                this.editVisible = false;
                this.$message.success(`修改第 ${this.idx+1} 行成功`);
            },
            // 确定删除
            deleteRow(){
                this.delUserBtn(this.tableData[this.idx].id);
                this.tableData.splice(this.idx, 1);
                this.$message.success('删除成功');
                this.delVisible = false;
            },
            //删除用户列表函数
            delUserBtn(id){
                this.$axios({
                    headers: {'access_token':this.token},
                    method: 'post',
                    url: 'Member/destroy',
                    data: {
                        id:id
                    }
                })
            }
        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
</style>
