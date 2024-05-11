<template>
  <div class="login-container">
    <van-nav-bar title="登录" />

    <van-form ref="loginForm" @submit="onSubmit">
      <van-field
        v-model="user.mobile"
        name="mobile"
        label="手机号"
        placeholder="请输入手机号"
        :rules="rules.mobile"
        type="number"
        maxlength="11"
        ><i slot="left-icon" class="toutiao toutiao-shouji"></i
      ></van-field>
      <van-field
        v-model="user.code"
        type="number"
        name="code"
        label="验证码"
        placeholder="验证码"
        :rules="rules.code"
        maxlength="6"
      >
        <i slot="left-icon" class="toutiao toutiao-yanzhengma"></i>

        <template #button>
          <!-- <van-count-down :time="1000 * 60" format="秒" /> -->
          <van-count-down
            v-if="isCountDownShow"
            :time="1000 * 10"
            format="ss s"
            @finish="isCountDownShow = false"
          />

          <van-button
            v-else
            size="small"
            type="primary"
            native-type="button"
            @click="sendSmsCode"
            >发送验证码</van-button
          >
        </template>
      </van-field>
      <div style="margin: 16px">
        <van-button block type="info" native-type="submit">提交</van-button>
      </div>
    </van-form>
  </div>
</template>

<script>
import { login, getSmsCode } from '../../api/user'
export default {
  name: '',
  // mixins: [],
  components: {},
  props: {},
  data () {
    return {
      user: {
        // mobile: "13911111111",
        // code: "246810",
        mobile: '',
        code: ''
      },
      // 对象，手机号和验证码两个属性、各个属性对应一个数组，数组里定义多个验证规则
      rules: {
        mobile: [
          { required: true, message: '请填写手机号' },
          {
            pattern: /^1[3|5|7|8]\d{9}$/,
            message: '手机号格式错误'
          }
        ],
        code: [
          { required: true, message: '请填写验证码' },
          {
            pattern: /^\d{6}$/,
            message: '验证码格式错误'
          }
        ]
      },
      isCountDownShow: false
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
    async onSubmit () {
      // 加载中的效果
      this.$toast.loading({
        duration: 0, // 持续时间，0表示持续展示不停止
        forbidClick: true, // 是否禁止背景点击
        message: '登录中...' // 提示消息
      })

      try {
        const res = await login(this.user)
        // console.log(res);
        this.$toast.success('登录成功')
        // token存入共享容器即Vuex里同时持久化到本地
        // setItem(data)
        this.$store.commit('setUser', res.data.data)
      } catch (err) {
        if (err.response.status === 400) {
          // console.log("登录失败", err);
          this.$toast.fail('登录失败，手机号或验证码错误')
        }
      }
    },
    async sendSmsCode () {
      // 校验手机号
      try {
        // vant 的表单组件中验证表单，支持传入 name 来验证单个或部分表单项
        await this.$refs.loginForm.validate('mobile')
      } catch (err) {
        return console.log('验证失败', err)
      }
      // 手机号校验通过就是倒计时
      this.isCountDownShow = true
      // 发送请求获取验证码
      try {
        await getSmsCode(this.user.mobile)
        this.$toast('发送成功')
      } catch (err) {
        console.log(err)
        // 发送失败，关闭倒计时
        this.isCountDownShow = false
        if (err.response.status === 429) {
          this.$toast('发送太频繁了，请稍后重试')
        } else {
          this.$toast('发送失败，请稍后重试')
        }
      }
    }
  }
}
</script>

<style scoped lang="less">
.login-container {
  .toutiao {
    font-size: 37px;
  }
}
</style>
