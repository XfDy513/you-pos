<template>
  <div id="add-site">
    <x-header
      slot="header"
      :left-options="{showBack: false}"
    >
      <a @click="goBack()" slot="left" class="backBox"><i class="iconfont icon-back"></i></a>
      修改地址
    </x-header>
    <div class="site-body">
      <div class="details">
        <div class="one">
          <!--<img src="../../assets/img/my/shouhuoren.png" alt="">-->
          <span>收&nbsp;&nbsp;货&nbsp;&nbsp;人：</span>
          <input type="text" placeholder="请输入收货人姓名" v-model="userName">
        </div>
        <div class="one">
          <!--<img src="../../assets/img/my/shoujihaoma.png" alt="">-->
          <span>手机号码：</span>
          <input type="text" placeholder="请输入手机号" v-model="mobile">
        </div>
        <div class="one" style="justify-content: space-between">
          <!--<img src="../../assets/img/my/diqu.png" alt="">-->
          <span>所在地区：</span>
          <x-address @on-hide="logHide" @on-show="logShow" title="" v-model="value" :list="addressData"
                     @on-shadow-change="onShadowChange" placeholder="请选择地址" :show.sync="showAddress" raw-value></x-address>
        </div>
        <div class="one" style="border:none">
          <span>详细地址：</span>
        </div>
        <div class="two">
          <textarea name="site" v-model="address" id="" cols="42" rows="3" placeholder="街道、街牌号等"></textarea>
        </div>
      </div>
      <div class="change">
        <i class="iconfont icon-roundcheckfill" @click="checkdefault" :style="{'color' : status == '1' ? '#ff7512' : '#a1a1a1'}"></i>
        <span>设为默认地址</span>
      </div>
      <div class="confirm" @click="submit">
        <button>确定</button>
      </div>
    </div>
  </div>
</template>

<script>
import { XHeader, XAddress, ChinaAddressV4Data } from 'vux'
import * as apiHttp from '../../api/index'
import address from '../../plugin/address'
export default {
  name: 'address1',
  components: {
    XHeader,
    XAddress,
    ChinaAddressV4Data
  },
  data () {
    return {
      status: this.$route.query ? this.$route.query.is_default : '',
      value: this.$route.query ? [this.$route.query.province, this.$route.query.city, this.$route.query.county] : [],
      addressAll: [],
      address: this.$route.query ? this.$route.query.detail_address : '',
      addressData: address,
      showAddress: false,
      userName: this.$route.query ? this.$route.query.name : '',
      mobile: this.$route.query ? this.$route.query.mobile : ''
    }
  },
  mounted: function () {
    // 删除
  },
  methods: {
    doShowAddress () {
      this.showAddress = true
      setTimeout(() => {
        this.showAddress = false
      }, 2000)
    },
    onShadowChange (ids, names) {
      this.addressAll = names
      this.value = names
    },
    checkdefault () {
      if (this.status === '1') {
        this.status = '0'
      } else {
        this.status = '1'
      }
    },
    submit () {
      let reg = 11 && /^((13|14|15|16|17|18|19)[0-9]{1}\d{8})$/
      if (!this.mobile) {
        this.$vux.toast.text('手机号不能为空')
        return
      }
      if (!reg.test(this.mobile)) {
        this.$vux.toast.text('手机号格式不对')
        return
      }
      if (this.value.length === 0) {
        this.$vux.toast.text('请选择地址')
        return
      }
      if (this.address.length === 0) {
        this.$vux.toast.text('请输入详细地址')
        return
      }
      console.log(this.value)
      apiHttp.getAddressEdit(this.userName, this.mobile, this.addressAll[0], this.addressAll[1], this.addressAll[2], this.address, this.status, this.$route.query.id).then(response => {
        if (response.code === 1) {
          this.$vux.toast.text(response.msg)
          this.goBack();
        } else {
          this.$vux.toast.text(response.msg)
        }
      })
    },
    logHide (str) {
      console.log('on-hide', str)
    },
    logShow (str) {
      console.log('on-show')
    }
  }
}
</script>

<style lang="less" scope>
@import "../../assets/less/common";
#add-site {
  .vux-cell-box:not(:first-child):before {
    border: none;
  }
  .site-body {
    margin-top: @margin-top;
    .details {
      padding: 0 0.3rem;
      background: #ffffff;
      margin-bottom: 0.1rem;
      padding-bottom: 0.4rem;
      .one {
        display: flex;
        justify-content: flex-start;
        align-items: center;
        height: 1rem;
        border-bottom: 1px solid @border-color;
        color: @font-middle-color;
        font-size: @font-big;
        img {
          width: 0.35rem;
          margin-right: 0.2rem;
        }
        input {
          border: none;
          height: 90%;
          text-align: left;
          margin-left: 0.5rem;
          outline: none;
          color: @font-middle-color;
          font-size: @font-big;
          width: 70%;
        }
      }
      .two {
        width: 94%;
        height: 1.5rem;
        margin: 0 auto;
        border-radius: 0.1rem;
        background: #f7f7f7;
        display: flex;
        justify-content: center;
        align-items: center;
        textarea {
          border: none;
          resize: none;
          background: #f7f7f7;
          outline: none;
          font-size: @font-nomal;
        }
      }
    }
    .change {
      height: 7.5vh;
      background: #ffffff;
      margin-top: 0.1rem;
      text-align: left;
      padding: 0 0.3rem;
      line-height: 7.5vh;
      color: @font-middle-color;
      font-size: @font-big;
      i {
        margin-right: 0.2rem;
      }
    }
  }
  .confirm {
    width: 84%;
    height: 0.9rem;
    margin: 1rem 8% 0;
    padding-bottom: 0.5rem;
    button {
      background: @main-red-color;
      width: 100%;
      height: 100%;
      border: none;
      color: #ffffff;
      font-size: 0.4rem;
      border-radius: 0.2rem;
      outline: none;
    }
  }
}
</style>

