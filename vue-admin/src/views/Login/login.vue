<template>
<!--template中主要存放html代码，其中的每一块内容都需要一个父元素包裹，可以是div以外的其他元素-->
    <div id ="login">
        <div class="login-wrap">
            <ul class="menu-tab">
                <!--:class绑定-->
                <li  v-for="item in menuTab" :key="item.id" :class="{'current': item.current}" @click="toggleMenu(item)">{{ item.txt }}</li>
                    <!--:key 元素的唯一标识，可以自行声明id,一般会默认添加-->
            </ul>
            <!--表单start-->
            <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form" size="medium">
            <el-form-item  prop="email" class="item-form">
                <label>邮箱</label>
                <el-input type="text" v-model="ruleForm.email" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item  prop="password" class="item-form">
                <label>密码</label>
                <el-input type="password" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
            </el-form-item>

            <el-form-item  prop="passwords" class="item-form" v-if="model === 'register'">
   <!--v-show 和 v-if的区别 v-show是将元素隐藏起来，使用v-if是将当前元素删除掉，如果当前元素中包含接口时，如果判定条件为true 会自动请求接口-->
                <label>重复密码</label>
                <el-input type="password" v-model="ruleForm.passwords" autocomplete="off" minlength="6" maxlength="20"></el-input>
            </el-form-item>

            <el-form-item  prop="code" class="item-form">
                <label>验证码</label>
                <el-row :gutter="10">
                    <el-col :span="15">
                    <el-input v-model.number="ruleForm.code" minlength="6" maxlength="20" ></el-input>
                    </el-col>
                    <el-col :span="9">
                        <el-button type = "success" class="block" >获取验证码</el-button>
                    </el-col>
                 </el-row>
            </el-form-item>
            <el-form-item>
                <el-button type="danger" @click="submitForm('ruleForm')" class="login-btn block">提交</el-button>
            </el-form-item>
            </el-form>
        </div>
    </div>
</template>
<script>
import { stripscript,IsEmail,validatePassword,validateCode } from '@/utils/validate'; //过滤特殊字符
export default {
     //vue开发是以数据驱动视图
    //js 是操作Dao元素
    name: 'login',
    data(){
        //验证码验证
        var checkCode = (rule, value, callback) => {
        this.ruleForm.code = stripscript(value); //过滤特殊字符
        value = this.ruleForm.code;
        if (value === '') {
          callback(new Error('请输入验证码'));
        } else if (validateCode(value)) {
          callback(new Error('验证码格式有误'));
        } else {
          callback();
        }
      };
      //验证邮箱
      var validateEmail = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入邮箱'));
        } else if(IsEmail(value)){
         callback(new Error('邮箱格式有误')); 
        } else{
            callback(); 
        }
      };
        //验证密码
      var validatePass = (rule, value, callback) => {
        this.ruleForm.password = stripscript(value);
        value = this.ruleForm.password;
        console.log(value);
        if (value === '') {
          callback(new Error('请输入密码'));
        } else if (validatePassword(value)) {
          callback(new Error('密码为6-20位的数字+字母'));
        } else {
          callback();
        }
      };
      //验证重复密码
        var validatePasswords = (rule, value, callback) => {
        this.ruleForm.passwords = stripscript(value);
        value = this.ruleForm.passwords;
        console.log(value);
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value!= this.ruleForm.password) {
          callback(new Error('两次输入密码不一致'));
        } else {
          callback();
        }
      };
     return {
         menuTab:[
                { txt:'登陆',current: true , type: 'login'},
                { txt:'注册',current: false, type: 'register'}
            ],
          ruleForm: {
          email: '',
          password: '',
          code: ''
        },
        //模块值
        model: '',//判定当前的模块时登陆还是注册
        rules: {
          email: [
            { validator: validateEmail, trigger: 'blur' } //失去焦点时触发
          ],
          password: [
            { validator: validatePass, trigger: 'blur' }
          ],
           passwords: [
            { validator: validatePasswords, trigger: 'blur' }
          ],
          code: [
            { validator: checkCode, trigger: 'blur' }
          ]
        }
      };
    },
    //data创建完成后执行
    created(){

    },
    //挂载完成后自动执行
    mounted(){
    },
    methods:{
        toggleMenu(data){
            this.menuTab.forEach(elem => {
                elem.current = false //触发函数后将两个部分都初始为false
            })
            data.current  = true //将当前选中的部分设置为true
            //修改当前模块的值
            this.model = data.type //点击不同模块显示不同的内容
        },
        submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
    }
    }
    //主要模块包含  数据data 数据创建完成时任务created 方法methods
    /*props:{
    },
    watch:{
    }*/
    //props watch 子主键获取父主键的参数
</script>
<!--lang=scss指明文件类型，scope指明作用范围，不加scope作用范围为全局-->
<style lang= "scss" scope>
 #login {
     height: 100vh;  
     background-color: #344a5f;
 }
 .login-wrap {
     width:330px;
     margin:auto;

 }
 .menu-tab{
     text-align:center;
     li {
         display: inline-block;
         width: 88px;
         line-height: 36px;
         font-size: 14px;
         color: #fff;
         border-radius: 2px;
         cursor: pointer;
         
     }
 }
.current {
         background-color: rgba(0,0,0, .1);
     }
.login-form { 
    margin-top: 29px;
    label {
         display: block;
         font-size: 14px;
         color: #fff;
    }
    .item-form { margin-bottom: 13px; }
    .block{
        display: block;
        width: 100%;
    }
    .login-btn{
        margin-top:19px;
    }
 }
</style>