<template>
  <div>
    <p>默认使用谷歌翻译免费接口，若结果不满意或接口失效请:
      <el-popover :visible="showInterface" placement="bottom" :width="400" trigger="click">
        <template #reference>
          <el-button type="primary" @click="showInterface = true" style="margin-right: 16px" text>切换</el-button>
        </template>
        <div>
          <el-radio v-model="interface_" label="google" value="google">谷歌</el-radio>
          <el-radio v-model="interface_" label="youdao" value="youdao">有道</el-radio>
          <el-radio v-model="interface_" label="baidu" value="baidu">百度</el-radio>
        </div>

        <div v-show="interface_ == 'youdao'">
          见<a href="https://ai.youdao.com/gw.s">有道翻译</a>官网
          <el-input v-model="youdao.appKey" placeholder="appKey" clearable></el-input>
          <el-input v-model="youdao.key" placeholder="Key" clearable></el-input>
        </div>
        <div v-if="interface_ == 'baidu'" style="flex">
          见<a href="http://api.fanyi.baidu.com/api/trans/product/prodinfo">百度翻译</a>官网
          <el-input v-model="baidu.appKey" placeholder="appKey" clearable></el-input>
          <el-input v-model="baidu.key" placeholder="Key" clearable></el-input>
        </div>
        <el-button type="primary" @click="saveToken" text>保存</el-button>

      </el-popover>
    </p>
    <p>去重等级:<el-select v-model="level" class="m-2" placeholder="Select">
        <el-option v-for="item in levelOptions" :key="item.value" :label="item.label" :value="item.value" />
      </el-select>
    </p>

    <el-row>
      <el-col :xs="12" :sm="12" :md="12" :lg="12" :xl="12">
        <div style="margin-right: 0;">
          <h4>转换前:</h4>
          <textarea v-model="start" placeholder="初级
> 中->英->德->中
中级
> 中->英->德->日->葡萄牙->中
高级
> 中->英->德->日->葡萄牙->意大利->波兰->保加利亚->爱沙尼亚->中
(将重复内容复制到此，点击开始转换，结果将在右侧显示)" class="textarea-inherit" style="background-color: palegreen;" name="" id="" cols="20"
            rows="10"></textarea>
          <br>
          <el-button type="primary" @click="queen" :loading="loading">开始转化</el-button>
        </div>
      </el-col>
      <el-col :xs="12" :sm="12" :md="12" :lg="12" :xl="12">
        <div>
          <h4>转换后:</h4>
          <textarea v-model="result" class="textarea-inherit" style="background-color: pink;" name="" id="" cols="20"
            rows="10"></textarea>
          <br>
          <el-button type="primary" @click="copy">一键复制</el-button>


        </div>
      </el-col>
    </el-row>
    <el-divider />
    <el-dialog v-model="showLog" title="Log">
      <p v-for="item in log">
        {{ item }}
      </p>
    </el-dialog>
    <el-button type="primary" @click="showLog = true">显示日志</el-button>
    <el-button type="primary" @click="showInfo()">说明文档</el-button>
    <div id="about">
      <a href="#about"></a>
      <About v-if="showAbout" />
    </div>
  </div>
</template>


<script>
import axios from 'axios'
import { ElMessage } from 'element-plus';
import Md from './md.vue'
import { sha256 } from 'js-sha256'
import md5 from 'js-md5'
export default {
  name: 'Translation',
  data() {
    return {
      loading: false,
      start: '',
      result: '',
      tmp: '',
      transList: {
        simple: ["zh en", "en de", "de zh"],
        middle: ["zh en", "en de", "de jp", "jp pt", "pt zh"],
        high: [
          "zh en",
          "en de",
          "de jp",
          "jp pt",
          "pt it",
          "it pl",
          "pl bul",
          "bul est",
          "est zh",
        ],
      },
      log: [],
      level: 'auto',
      showLog: false,
      levelOptions: [
        {
          value: 'auto',
          label: '智能检测',
        },
        {
          value: 'simple',
          label: '简单',
        },
        {
          value: 'middle',
          label: '中等',
        },
        {
          value: 'high',
          label: '高级',
        },
      ],
      showAbout: false,
      interfaceList: [
        'google',
        'baidu',
        'youdao',
      ],
      interface_: 'google',
      showInterface: false,
      youdao: {
        appKey: '',
        key: ''
      },
      baidu: {
        appKey: '',
        key: ''
      },
    }
  },
  created() { },
  methods: {
    queen() {
      if(this.start.length == 0) {
        ElMessage.error('请输入内容');
        return;
      }
      this.loading = true
      this.log = []
      let level = this.level
      if (this.level == 'auto') {
        level = this.getLevel()
      }
      var dst = this.start;
      (async () => {
        for (let i = 0; i < this.transList[level].length; i++) {
          var lang = this.transList[level][i].split(" ");
          let tmp = await this.translate(dst, lang[0], lang[1]);
          dst = tmp;
          let log = `${lang[0]} to ${lang[1]}: ${tmp}`
          console.log(log);
          this.log.push(log);
          this.result = dst;
        }
        this.loading = false
      })();
    },
    translate(q, sl = 'auto', tl) {
      return new Promise((resolve, reject) => {
        if (this.interface_ === 'google') {
          this.googleTranslate(q, sl, tl).then(res => {
            resolve(res)
          })
        }
        else if (this.interface_ === 'baidu') {
          this.baiduTranslate(q, sl, tl).then(res => {
            resolve(res)
          })
        }
        else {
          this.youdaoTranslate(q, sl, tl).then(res => {
            resolve(res)
          })
        }
      }
      )
    },
    googleTranslate(q, sl = 'auto', tl) {
      return new Promise((resolve, reject) => {
        let url = `https://translate.googleapis.com/translate_a/single?client=gtx&sl=${sl}&tl=${tl}&dj=1&dt=t&dt=bd&dt=qc&dt=rm&dt=ex&dt=at&dt=ss&dt=rw&dt=ld&q=${q}`
        axios.get(
          url,
          {
            headers: {
              'Content-Type': 'application/json',
            },
          }
        ).then(res => {
          let sentences = res.data.sentences
          //将结果拼接
          let tmp = ''
          sentences.forEach(item => {
            if (item.trans) {
              tmp += item.trans
            }
          })
          console.log(tmp);
          resolve(tmp)
        })
      })
    },
    baiduTranslate(q, sl = 'auto', tl) {
      return new Promise((resolve, reject) => {
        var url = "改为你的百度翻译API地址"
        try {
          q = q.replace(/\n/g, ' ')
        } catch (error) {
          console.log(error);
        }
        var data = {
          q: q,
          from: sl,
          to: tl,
          appid: this.baidu.appKey,
          salt: "",
          sign: "",
        };
        data.salt = Math.floor(Math.random() * 10000000000);
        data.sign = md5(this.baidu.appKey + q + data.salt + this.baidu.key);
        axios.post(url, data).then(res => {
          resolve(res.data)
        })
      })
    },
    youdaoTranslate(q, sl = 'auto', tl) {
      return new Promise((resolve, reject) => {
        var url = "这里改为你的有道翻译API地址"
        axios.post(url, {
          q: q,
          sl: sl,
          tl: tl,
          APP_KEY: this.youdao.appKey,
          APP_SECRET: this.youdao.key
        }).then(res => {
          resolve(res.data)
        })
      })
    },
    copy() {
      //this.result为空，则提示.
      if (this.result.length == 0) {
        ElMessage.warning('请先转换内容');
        return;
      }

      //把form.path_list内容复制到剪贴板，用'\n'分隔
      let str = this.result;
      //创建一个textarea元素
      let textarea = document.createElement('textarea');
      //设置textarea的属性
      textarea.setAttribute('id', 'textarea');
      textarea.setAttribute('style', 'position:fixed;top:0;left:0;width:0;height:0;');
      //把内容放到textarea中
      textarea.value = str;
      //把textarea插入到body中
      document.body.appendChild(textarea);
      //选中textarea中的内容
      textarea.select();
      //复制
      document.execCommand('copy');
      //删除textarea
      document.body.removeChild(textarea);
      ElMessage.success('复制成功');
    },
    getLevel() {
      let level = 'simple'
      let tmp = this.start.replace(/\s/g, '')
      if (tmp.length < 100) {
        level = 'middle'
      }
      if (tmp.length < 50) {
        level = 'high'
      }
      return level
    },
    truncate(q) {
      var len = q.length;
      if (len <= 20) return q;
      return q.substring(0, 10) + len + q.substring(len - 10, len);
    },
    saveToken() {
      ElMessage
      this.showInterface = false
      //将this.baidu.appKey和this.baidu.key存入localStorage
      localStorage.setItem('baidu', JSON.stringify(this.baidu))
      //将this.youdao.appKey和this.youdao.key存入localStorage
      localStorage.setItem('youdao', JSON.stringify(this.youdao))
    },
    showInfo() {
      this.showAbout = !this.showAbout
      if (this.showAbout) {
        //跳转到a标签#about
        window.location.hash = '#about'
      }
    }
  },
  components: {
    About: Md,
  },
  mounted() {
    console.log('获取localStorage中的数据');
    //获取localStorage中的baidu和youdao
    this.baidu = JSON.parse(localStorage.getItem('baidu'))
    this.youdao = JSON.parse(localStorage.getItem('youdao'))
    //如果localStorage中没有baidu和youdao，则设置默认值
    if (!this.baidu) {
      this.baidu = {
        appKey: '',
        key: '',
      }
    }
    if (!this.youdao) {
      this.youdao = {
        appKey: '',
        key: '',
      }
    }
    //获取localStorage中的level，若没有，则设置为auto
    this.level = localStorage.getItem('level') || 'auto'
    //获取localStorage中的interface_，若没有，则设置为google
    this.interface_ = localStorage.getItem('interface_') || 'google'

  },
  watch: {
    level() {
      //如果level发生变化，则本地存储level
      localStorage.setItem('level', this.level)
    },
    interface_() {
      //如果interface_发生变化，则本地存储interface_
      localStorage.setItem('interface_', this.interface_)
    }
  }
}

</script>
<style scoped>
div {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.textarea-inherit {
  width: 80%;
  overflow: auto;
  word-break: break-all;
}
</style>
