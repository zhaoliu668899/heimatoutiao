<template>
  <div class="login-container">
    <van-nav-bar title="登录" />

    <van-form @submit="onSubmit">
      <van-field
        v-model="mobile"
        name="mobile"
        label="手机号"
        placeholder="请输入手机号"
        :rules="[{ required: true, message: '请填写手机号' }]"
        type="number"
        ><i slot="left-icon" class="toutiao toutiao-shouji"></i
      ></van-field>
      <van-field
        v-model="code"
        type="number"
        name="验证码"
        label="验证码"
        placeholder="验证码"
        :rules="[{ required: true, message: '请填写验证码' }]"
        ><i slot="left-icon" class="toutiao toutiao-yanzhengma"></i
        ><template #button>
          <van-button size="small" type="primary">发送验证码</van-button>
        </template></van-field
      >
      <div style="margin: 16px">
        <van-button block type="info" native-type="submit">提交</van-button>
      </div>
    </van-form>
  </div>
</template>

<script>
export default {
  name: '',
  // mixins: [],
  components: {},
  props: {},
  data () {
    return {
      mobile: '',
      code: ''
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
    onSubmit (values) {
      console.log('submit', values)
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
