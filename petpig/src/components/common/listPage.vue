<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i>{{listTitslots}}</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="addMore">添加</el-button>
                <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="search">搜索</el-button>
            </div>
            <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column v-for="(itemList,index) in pagLisNames" :prop="itemList.value1" :label="itemList.value2" width="150">
                </el-table-column>
                <el-table-column label="操作" width="180">
                    <template slot-scope="scope">
                        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="total">
                </el-pagination>
            </div>
        </div>
         <!-- 新增弹出框 -->
        <el-dialog title="" :visible.sync="addVisible" width="40%">
           <el-form ref="form" :model="form" label-width="100px">
                <el-form-item v-for="(addList,index) in addListData" :label="addList.value1">
                    <el-input v-model="formData[addList.value2]"></el-input>
                </el-form-item>
                <el-form-item label="状态">
                    <el-radio v-model="form.status" label="1">正常</el-radio>
                    <el-radio v-model="form.status" label="2">禁止</el-radio>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="addVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveAddEdit">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 编辑弹出框 -->
        <el-dialog title="" :visible.sync="editVisible" width="40%">
           <el-form ref="form" :model="form" label-width="100px">
               <el-form-item v-for="(editList,index) in editListData" :label="editList.value1">
                    <el-input v-model="formData[editList.value2]"></el-input>
                </el-form-item>
                <el-form-item label="状态">
                    <el-radio v-model="form.status" label="1">正常</el-radio>
                    <el-radio v-model="form.status" label="2">禁止</el-radio>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
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
        props:{
            //接收列表标题数据
            listTitslots:{
                type:String,
                default:''
            },
            //接收列表头数据
            pagLisNames:{
                type:Array,
                default:[{
                }]
            },
            //接收添加弹框数据
            addListData:{
                type:Array,
                default:[{
                }]
            },
            //接收编辑弹框数据
            editListData:{
                type:Array,
                default:[{
                }]
            },
            formData:{
                type:Object,
                default:{
                }
            },
            //接收请求Url接口地址
            getDataUrl:{
                type:Object,
                default:{
                }
            }
        },
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
                addVisible:false,
                editVisible: false,
                delVisible: false,
                idx: -1,
                form: {
                    id: '',
                    pid: '',
                    name: '',
                    code: '',
                    remark: '',
                    status: '1',
                    sort:'',
                    default:''
                },
            }
        },
        created() {
            this.token=localStorage.getItem("token");
            console.log(this.token);
            this.getData();
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
                    url: this.getDataUrl.listurl,
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
            //新增
            addMore(){
                this.addVisible = true;
            },
            //编辑按钮
            handleEdit(index, row) {
                this.idx = index;
                const item = this.tableData[index];
                for(var key in this.formData){
                    formData.key=item.key
                }
                // this.form = {
                //     id: item.id,
                //     name: item.name,
                //     status: item.status.toString(),
                //     sort:item.sort,
                //     default:item.default
                // }
                this.editVisible = true;
            },
            //删除按钮
            handleDelete(index, row) {
                this.idx = index;
                this.delVisible = true;
            },
            //批量删除
            delAll() {
                const length = this.multipleSelection.length;
                // let str = '';
                this.del_list = this.del_list.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    this.delUserBtn(this.multipleSelection[i].id);
                    // str += this.multipleSelection[i].name + ' ';
                }
                this.$message.success('批量删除成功');
                this.multipleSelection = [];
                this.getData();
            },
            //批量选中
            handleSelectionChange(val) {
                this.multipleSelection = val;
                console.log(val);
            },
            //保存添加
            saveAddEdit(){
                this.$axios({
                    headers: {'access_token':this.token},
                    method: 'post',
                    url: this.getDataUrl.addurl,
                    data: {
                        name: this.form.name,
                        status: this.form.status,
                        sort:this.form.sort,
                        default:this.form.default
                    }
                }).then(res=>{
                  console.log(res);
                  this.getData();
                })
                this.addVisible = false;
                this.$message.success(`添加成功`);
            },
            // 保存编辑
            saveEdit() {
                this.$axios({
                    headers: {'access_token':this.token},
                    method: 'post',
                    url: this.getDataUrl.editurl,
                    data: {
                        id: this.form.id,
                        name: this.form.name,
                        status: this.form.status,
                        sort:this.form.sort,
                        default:this.form.default
                    }
                }).then(res=>{
                  console.log(res);
                })
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
                    url: this.getDataUrl.delturl,
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
