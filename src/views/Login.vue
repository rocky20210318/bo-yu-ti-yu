<template>
    <div id="login">
        <van-nav-bar
            :border="false"
            @click-left="$router.history.go(-1)"
            placeholder
            left-arrow
        />
        <img src="../assets/login-bg-1.png" class="bg-1">
        <img src="../assets/login-bg-2.png" class="bg-2">
        <div class="content">
            <!-- <h1>登陆</h1> -->
            <div class="content-inner">
                <!-- <img src="../assets/login-img-1.png" class="img-bg"> -->
                <div class="label-input">
                    <!-- <label class="label">账号</label> -->
                    <field
                        placeholder="请输入手机号码"
                        type="tel"
                        v-model="phoneNumber"
                        clearable
                    />
                </div>
                <div class="label-input">
                    <!-- <label class="label">密码</label> -->
                    <Field
                        :type="inputTypeCon"
                        placeholder="输入8-16位英文与字母构成的密码"
                        v-model="valuePwd"
                    />
                </div>
                <router-link to="/password-reset" class="forgotPwd">忘记密码?</router-link>
            </div>
            <Button color="linear-gradient(45deg, #90B8FF 0%, #1864FF 100%)"
                :disabled="!(phoneTest && valuePwd)"
                @click="submitButton"
                :loading="loginLoading"
                loading-text="登陆中"
                block
                type="primary"
                class="button">登陆</Button>
            <van-row type="flex" align="center" justify="center" class="tipsBox">
                <checkbox v-model="checked" checked-color="linear-gradient(45deg, #90B8FF 0%, #1864FF 100%)" icon-size="0.38rem" class="checkbox" />
                <p>我已阅读并同意<router-link class="privacy" to="/privacy">《隐私政策》</router-link>与<router-link class="privacy" to="/agreement">《用户协议》</router-link></p>
            </van-row>
            <p class="switchPage"><router-link to="/sign-up">注册</router-link></p>
        </div>
    </div>
</template>

<script>
import { Button, Field, Checkbox } from 'vant'
import AV from 'leancloud-storage'

export default {
    name: 'Login',
    components: {
        Button,
        Field,
        Checkbox
    },
    data () {
        return {
            phoneNumber: '',
            eyeCon: true,
            loginLoading: false,
            valuePwd: '',
            checked: false
        }
    },
    computed: {
        phoneTest () {
            const PHONE_EXP = /^(((13[0-9])|(14[5-7])|(15[0-9])|(16[0-9])|(17[0-9])|(18[0-9])|(19[0-9]))+\d{8})$/
            const { phoneNumber } = this
            if (!PHONE_EXP.test(phoneNumber)) {
                return false
            } else return true
        },
        inputTypeCon () {
            return this.eyeCon ? 'password' : 'text'
        }
    },
    mounted () {
    },
    methods: {
        async submitButton () {
            const password = /^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{8,16}$/
            if (!this.checked) {
                this.$toast('请阅读并同意《隐私政策》与《用户协议》')
                return false
            }
            if (password.test(this.valuePwd)) {
                this.loginLoading = true
                await AV.User.logInWithMobilePhone(this.phoneNumber, this.valuePwd).then(() => {
                    this.$toast('登陆成功，稍后自动跳转')
                    setTimeout(() => {
                        this.$router.push('/my')
                    }, 2 * 1000)
                }, () => {
                    this.$toast('网络异常请稍后重试')
                })
                this.loginLoading = false
            } else this.$toast('密码格式为8-16位包含字母及数字')
        }
    }
}
</script>
<style lang="scss">
#login {
    .van-nav-bar {
        background: rgba(0,0,0,0);
    }
    .van-nav-bar .van-icon {
        color: #fff;
    }
     .label-input .van-cell {
        padding: 25px;
        background-color: #f5f5f5;
        border-radius: 10px;
        input {
            font-size: 28px;
        }
    }
}
</style>
<style scoped lang="scss">
#login{
    position: relative;
    height: 100%;
    background: #fff;
    // background: url('../assets/login-bg.png') no-repeat;
    // background-size: 100% auto;
    // padding-left: 20px;
    // padding-right: 20px;
    .bg-1 {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
    }
    .bg-2 {
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
    }
}
.content {
    position: relative;
    height: 60%;
    margin: 200px 30px 100px;
    // background-color: #ffffff;
    // box-shadow: 0px 2px 4px 0px rgba(0, 0, 0, 0.12);
    border-radius: 20px;
    padding: 140px 45px 0px;
    .img-bg {
        position: absolute;
        top: -248px;
        z-index: 2;
        left: 0;
        right: 0;
        width: 453px;
        margin: auto;
    }
}
h1 {
    margin-bottom: 90px;
    font-size: 44px;
    color: #262628;
}
.label-input {
    margin-bottom: 24px;
    .label {
        display: block;
        margin-bottom: 20px;
        font-size: 28px;
        letter-spacing: 1px;
        color: #9b9b9b;
    }
}
.forgotPwd {
    margin: -10px 0 20px;
    display: block;
    text-align: right;
    font-size: 24px;
    color: #666;
}
.button {
    box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.15);
    border-radius: 32px;
}

.tipsBox {
    margin: 40px 0;
    font-size: 24px;
    color: #909090;
    .checkbox {
        margin-right: 5px;
    }
    a {
        color: #4990e2;
    }
}
.switchPage {
    font-size: 28px;
    color: #666;
    text-align: center;
}
</style>
