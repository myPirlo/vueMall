<template>
    <div>
        <header class="header">
                        <img class="navbar-brand-logo" src="static/logo1.png">
                        <div class="header-right">
                                    <span v-if="isLogin">欢迎您 {{userName}}</span>
                                    <a href="javascript:;" v-if="!isLogin"  @click="showLogin">Login</a>
                                    <a href="javascript:;" v-if="isLogin"  @click="loginOut">LoginOut</a>
                                    <a href="javascript:;">Register</a>
                                    <img class="navbar-brand-logo" src="static/cart.png">
                                    <router-link to="/cart">
                                         <span v-if="isLogin" class="cartIcons">{{cartCount}}</span>
                                    </router-link>
                                   
                                    
                        </div>
                            <div class="md-modal modal-msg md-modal-transition" v-bind:class="{'md-show':isShowLogin}">
                                <div class="md-modal-inner">
                                    <div class="md-top">
                                    <div class="md-title">Login in</div>
                                    <button class="md-close" @click="isShowLogin=false;">Close</button>
                                    </div>
                                    <div class="md-content">
                                    <div class="confirm-tips">
                                        <div class="error-wrap">
                                        <span class="error error-show" v-show="showtip01">用户名或者密码错误</span>
                                        <span class="error error-show" v-show="showtip02">请输入完整的登录信息</span>
                                        </div>
                                        <ul>
                                        <li class="regi_form_input">
                                            <i class="icon IconPeople"></i>
                                            <input type="text" tabindex="1" name="loginname"  v-model="userName" class="regi_login_input regi_login_input_left" placeholder="User Name" data-type="loginname">
                                        </li>
                                        <li class="regi_form_input noMargin">
                                            <i class="icon IconPwd"></i>
                                            <input type="password" tabindex="2"  name="password" v-model="userPwd" class="regi_login_input regi_login_input_left login-input-no input_text" placeholder="Password" @keyup.enter="login">
                                        </li>
                                        </ul>
                                    </div>
                                    <div class="login-wrap">
                                        <a href="javascript:;" class="btn-login" @click="login">登  录</a>
                                    </div>
                                    </div>
                                </div>
                                </div>
                                <div class="md-overlay" v-if="isShowLogin"></div>
        </header>
        <!-- <div class="header02 header-right">
                    <a href="javascript:;" @click="showLogin">Login</a>
                    <a href="">Register</a>
                    <img class="navbar-brand-logo" src="static/cart.png">
        </div> -->
    </div>
</template>
<style>
 .cartIcons{
    width: 25px;
    height: 25px;
    background: rgba(0,0, 0, 0.5);
    color: white;
    text-align: center;
    line-height: 25px;
    border-radius: 50%;
    position: relative;
    top: -12px;
    left: -15px;
    display: inline-block;
    cursor: pointer;
     
 }
    
</style>


<script>


import '../assets/css/login.css'
import axios from 'axios'
export default {
   data() {
       return {
           isShowLogin:false,
           showtip01:false,
           showtip02:false,
           userName:'admin',
           userPwd:'123456',
           isLogin:false,
           showCartCount:false
       }
   },
   computed:{
       cartCount(){
            return this.$store.state.cartCount
       }
   },
   mounted(){
       if(document.cookie){
           this.isLogin=true
       }
       this.getCartCount();
       
       
   },
   methods: {
       showLogin() {
           this.isShowLogin=true;
       },
       login(){
           if(this.userName==''||this.userPwd==''){
               this.showtip02=true;
           }else{
               this.showtip02=false;
               axios.post("/users/login",{
                  userName:this.userName,
                  userPwd:this.userPwd
               }).then((response)=>{
                    let res = response.data;
                    console.log(res)
                    if(res.status=="0"){
                      this.showtip01 = false;
                      this.isShowLogin = false;
                      this.isLogin=true;
                      this.showCartCount=true;

                      this.getCartCount();
                    }else{
                      this.showtip01 = true;
                    }
            });
           }
       },
       loginOut(){

            this.$router.push({path:"/"})
            axios.post("/users/loginOut",{
                  userName:this.userName,
                  userPwd:this.userPwd
               }).then((response)=>{
                    let res = response.data;
                    if(res.status=="0"){
                          this.isLogin=false;
                          this.showCartCount=false;
                    }
            });
       },
       getCartCount(){
           axios.get("/users/getCartCount").then((response)=>{
               let res=response.data;
               this.$store.commit("initCount",res.result);
           })
       }
       
   },

};
</script>
