<template>
    <div>
        <!-- 按钮 -->
        <div>
       <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
       <el-button type="danger" size="small">批量删除</el-button>
    </div>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="employees">
        <el-table-column label="编号" prop="id" fixed="left"></el-table-column>
        <el-table-column label="用户名" prop="username" fixed="left"></el-table-column>
        <el-table-column label="姓名" prop="realname" ></el-table-column>
        <el-table-column label="性别" prop="gender"></el-table-column>
        <el-table-column label="手机号" prop="telephone"></el-table-column>
        <el-table-column label="身份证号" prop="idCard"></el-table-column>
        <el-table-column label="银行卡号" prop="bankCard"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

                </template>  
        </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50">
    </el-pagination>
        <!-- /分页 -->
        <!-- 模态框 -->
    <el-dialog :title ="title" :visible.sync="visible" width="60%">
       <el-form label-width="80px" :model="from">
          <el-form-item label="用户名">
            <el-input v-model="form.username"/>
          </el-form-item>
          <el-form-item label="密码">
            <el-input type="password" v-model="form.password"></el-input>
          </el-form-item>
          <el-form-item label="姓名">
             <el-input v-model="form.realname"></el-input>
          </el-form-item>
          <el-form-item label="性别" v-model="form.gener">
           <template>
               <el-radio-group>
                    <el-radio :label="男">男</el-radio>
                    <el-radio :label="女">女</el-radio>
               </el-radio-group>
            </template>
            </el-form-item>
        <el-form-item label="银行卡号">
            <el-input v-model="form.bankCard"></el-input>
         </el-form-item>
            <el-form-item label="身份证号">
                <el-input v-model="form.idCard"></el-input>
         </el-form-item>
        <el-form-item label="手机号">
             <el-input v-model="form.telephone"></el-input>
        </el-form-item>
    </el-form>
     <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submintHandler">确 定</el-button>
  </span>
</el-dialog>
        <!-- /模态框 -->
    </div>
</template>

<script>
import request from '@/utils/request'//自定义库
import querystring from 'querystring'//系统库
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
     created(){
            this.loadData();
        },
    methods:{

        submintHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{//高速后台即将传递查询字符串，在请求体中存在
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form) 
            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
       
        loadData(){
            let url = "http://localhost:6677/waiter/findAll"     
             request.get(url).then((response)=>{
        this.employees = response.data;
    })
        },
        toDeleteHandler(id){
            //确认？
            this.$confirm('此操作将永久删除该成员, 是否继续？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '成功删除'
          });
        })
        },
        toUpdateHandler(row){
             this.form=row;
            this.title = "修改员工信息";
            this.visible=true;
        },
        toAddHandler(row){
             this.title = "添加员工信息"; this.visible=true;
            this.form={
                type:"employees"}
        },
        closeModalHandler(){
            this.visible=false;
        }

    }

}
</script>

<style scoped>

</style>