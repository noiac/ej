<template>
    <div>
      <!-- 员工管理 -->
      <h2>员工管理</h2>
        <!-- 按钮 -->
        <el-button size="small" type="primary" @click="toAddHandel">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column    fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left"  prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column fixed="right" label="操作">
             <!-- slot用来获取当前行的数据 -->
            <template v-slot="slot">
                <i class="el-icon-delete"  a href="" @click.prevent="toDeleteHandler(slot.row.id)"></i>
                <i class="el-icon-edit" a href="" @click.prevent="toUpdateHandler(slot.row)"></i>
            </template>
            </el-table-column>
            </el-table>
        <!-- 分页 -->
        <el-pagination
        layout="prev, pager, next"
        :total="50">
        </el-pagination>
        <!-- 模态框 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%"
        >
        <el-form :model="form" label-width="80px">
         <el-form-item label="用户名">
             <el-input v-model="form.username"></el-input>
         </el-form-item>
         <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item> 
        <el-form-item label="真实姓名">
          <el-input  v-model="form.realname"></el-input>
        </el-form-item> 
        <el-form-item label="性别">
            <el-radio-group  v-model="form.gender">
          <el-radio label="男">男</el-radio>
          <el-radio label="女">女</el-radio>
            </el-radio-group>
        </el-form-item> 
        <el-form-item label="手机号码">
          <el-input  v-model="form.telephone"></el-input>
        </el-form-item> 
        
        
        <el-form-item label="身份证号">
          <el-input  v-model="form.idCard"></el-input>
        </el-form-item> 
        <el-form-item label="银行卡号">
          <el-input  v-model="form.bankCard"></el-input>
        </el-form-item> 

        </el-form>



        <span slot="footer" class="dialog-footer">
            <el-button @click="closeModalHandler" >取 消</el-button>
            <el-button type="primary" @click="submitHandler" >确 定</el-button>
        </span>
        </el-dialog>
    </div>
</template>

<script>
import request from'@/utils/request'
import querystring from 'querystring'
export default {
    created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  },
    data(){
        return{
      visible:false,
      employees:[],
      form:{
        type:"waiter"
        }
    }  
    
},

methods:
{
    loadData(){
      let url = "http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.employees = response.data;
      })
    },
    submitHandler(){
        //   调用后台接口，并携带参数
        let url="http://localhost:6677/waiter/saveOrUpdate"
        request({
            url,
            method:"POST",
            headers:{
                "Content-Type":"application/x-www-form-urlencoded"
            },
            data:querystring.stringify(this.form)
            // 交互完毕
        }).then((response)=>{
            // 模态框关闭
            this.closeModalHandler();
            this.$loadData();
            this.$message({
                type:"success",
                message:response.message
            })
        })
      },

     toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url="http://localhost:6677/waiter/deleteById?id="+id;
        request.get(url).then((response)=>{
          this.loadData();
          //刷新结果
          //提示结果
           this.$message({
          type: 'success',
          message: response.message
        });
        });
       
      })
      
    },
        toAddHandel(){
        this.form={
        type:"customer"
      }
      this.visible = true;
    },
    toUpdateHandler(row){
         //在模态框中显示当前行信息
      this.form=row;
      this.visible = true;

    },
    closeModalHandler(){
      this.visible = false;
    }
}
}

</script>
<style scoped>

</style>
