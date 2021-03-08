<template>
  <div class="home">
    <b-row class="mx-1 mt-4">
      <b-col md="3" lg="4"></b-col>
      <b-col md="6" lg="4">
        <div>
          <b-jumbotron header="IP状态" lead="基于Wikihost IP检测API的Vue.js前端" bg-variant="light" border-variant="gray">
            <b-input-group class="mt-3">
              <b-form-input required @keyup.enter="check_ip" placeholder="输入需要查询的IP" v-model.trim="ip_to_check"></b-form-input>
              <b-input-group-append>
                <b-button @click="check_ip" variant="outline-info" :disabled="btn_disable"><b-icon :icon="btn_icon" :animation="btn_animation"></b-icon></b-button>
              </b-input-group-append>
            </b-input-group>
            <b-jumbotron v-if="issued" class="mt-3" bg-variant="white" border-variant="gray">
              <div v-if="!res.success">
                您的请求有误，或服务暂时不可用
              </div>
              <div v-if="res.success">
                IP: {{ this.ip_checked }} (ICMP测试)<br/>
                境内：<b-icon v-if="!res_returned" variant="secondary" icon="arrow-repeat" animation="spin"></b-icon><b-icon v-if="res_returned" :icon="in_icon" :variant="in_variant"></b-icon> |
                境外：<b-icon v-if="!res_returned" variant="secondary" icon="arrow-repeat" animation="spin"></b-icon><b-icon v-if="res_returned" :icon="out_icon" :variant="out_variant"></b-icon>
              </div>
            </b-jumbotron>
            <b-alert
              class="mt-2"
              :show="dismiss_count_down"
              dismissible
              variant="warning"
              @dismissed="dismiss_count_down=0"
              @dismiss-count-down="count_down_changed"
            >
              <p>IP地址格式错误！</p>
              <b-progress
                variant="warning"
                :max="dismiss_secs"
                :value="dismiss_count_down"
                height="4px"
              ></b-progress>
            </b-alert>
          </b-jumbotron>
        </div>
      </b-col>
      <b-col md="3" lg="4"></b-col>
    </b-row>
  </div>
</template>

<script>
const axios = require('axios').default

export default {
  name: 'Home',
  data: function () {
    return {
      dismiss_secs: 5,
      dismiss_count_down: 0,
      show_dismissible_alert: false,
      ip_pattern: /^((2(5[0-5]|[0-4]\d))|[0-1]?\d{1,2})(\.((2(5[0-5]|[0-4]\d))|[0-1]?\d{1,2})){3}$/,
      ip_to_check: '',
      ip_checked: '',
      issued: false,
      res_returned: false,
      please_wait: '请稍候。。',
      error_msg: '',
      res: {
        success: true,
        in: false,
        out: false
      }
    }
  },
  methods: {
    check_ip: function () {
      if (!this.ip_pattern.test(this.ip_to_check)) {
        this.show_alert()
      } else {
        this.issued = true
        this.res_returned = false
        this.res.success = true
        this.ip_checked = this.ip_to_check
        axios.get(`https://api-v2.50network.com/modules/ipcheck/icmp?ipv4=${this.ip_to_check}`)
          .then(response => {
            const res = response.data
            this.res.success = res.success
            this.res.in = res['firewall-enable']
            this.res.out = res['firewall-disable']
            this.res_returned = true
          })
          .catch(error => {
            this.error_msg = error
            this.res.success = false
            this.res_returned = true
          })
      }
    },
    count_down_changed: function (dismissCountDown) {
      this.dismiss_count_down = dismissCountDown
    },
    show_alert: function () {
      this.dismiss_count_down = this.dismiss_secs
    }
  },
  computed: {
    in_icon: function () {
      return this.res.in ? 'check-circle' : 'x-circle'
    },
    in_variant: function () {
      return this.res.in ? 'success' : 'danger'
    },
    out_icon: function () {
      return this.res.out ? 'check-circle' : 'x-circle'
    },
    out_variant: function () {
      return this.res.out ? 'success' : 'danger'
    },
    btn_disable: function () {
      if (!this.issued) {
        return false
      } else if (!this.res_returned) {
        return true
      } else {
        return false
      }
    },
    btn_animation: function () {
      if (this.btn_disable) {
        return 'cylon'
      } else {
        return ''
      }
    },
    btn_icon: function () {
      if (this.btn_disable) {
        return 'three-dots'
      } else {
        return 'search'
      }
    }
  }
}
</script>
