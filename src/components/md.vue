<template>
    <div class="article">
        <div class="body">
            <v-md-preview :text="str"></v-md-preview>
        </div>
    </div>

</template>

<script>
export default {
    data() {
        return {
            str: `# 有关论文翻译去重的一些说明
> From: 方源 2022-5-16 22:23:32
## 如何使用？

示例网站:[demo](http://translate.fangyuan99.xyz)

将需要去重的段落复制到左侧文本框，点击开始转化，等待右边文本框变成中文且数据不在变换后，点击一键复制即可。

注：默认使用的是免费的谷歌翻译接口，不确保可用性，建议自己申请有道翻译或百度翻译appkey和appid。（百度和有道的跨域问题我是用自己的服务器做中介新建的接口，所以可能用不了，希望有大牛能解决这个问题）

## 去重等级是什么意思？

**智能检测**

> 根据字符串长度自动选择去重等级

**初级**

> 中->英->德->中

**中级**

> 中->英->德->日->葡萄牙->中

**高级**

> 中->英->德->日->葡萄牙->意大利->波兰->保加利亚->爱沙尼亚->中

## 从源码编译

\`\`\`
git clone 
yarn 
yarn add element-plus
yarn add js-sha256 //若要使用百度接口，则需要安装这个包
yarn dev
\`\`\`


## 效果展示（去重等级：智能检测）

原文:

> 本毕业论文在指导老师的悉心指导和严格要求下已完成，从课题选取到具体的写作过程，论文初稿与定稿无不凝聚着她的心血和汗水。

谷歌接口

> 本论文是在教授的悉心指导和严格要求下完成的。从选题到具体的写作过程，论文的初稿和定稿都表达了他们的付出和汗水。

有道接口

>在老师的指导下，第一稿和最后一稿都是由她的努力和汗水支撑的，从选择的主题到具体的写作流程。

百度接口

> 这篇本科论文是在老师的精心指导和严格要求下完成的。从选题到实际写作过程，这篇文章的初稿和定稿凝聚了他的辛勤劳动和汗水。

## 数据安全

源码公开，数据仅保存在用户浏览器，不会上传到云端。

## 用到的开源项目

[一个 Vue 3 UI 框架 | Element Plus (element-plus.org)](https://element-plus.org/zh-CN/)

[汉堡包很棒棒/paper-conversion (gitee.com)](https://gitee.com/hamburgers_are_great/paper-conversion)`
        }
    }
}
</script>
<style scoped>
* {
    -webkit-font-smoothing: antialiased;
    cursor: default !important
}



img {
    text-align: center;
    border-radius: 2px;
    max-width: 100%
}

.shadow {
    box-shadow: 0 2px 3px rgba(0, 0, 0, .2)
}


h3 {
    padding: 0;
    margin: 0;
    margin-top: 30px;
    border-top: solid 1px #eee
}

.author {
    font-size: 12px;
    color: #999;
    font-size: 14px
}

.footer {
    padding: 40px 0;
    text-align: center;
    color: #ccc
}

.footer span {
    border: 1px solid #eee;
    border-radius: 2px;
    padding: 2px 8px;
    font-size: 12px;
    font-family: Helvetica Neue, Helvetica, Arial, sans-serif
}

.body {
    font-size: 15px;
    padding-top: 10px
}

.article {
    margin: 0 auto;
    max-width: 677px
}

.center {
    text-align: center
}

.full-width {
    width: 100%;
    max-width: 100%
}

li {
    line-height: 1em
}

p {
    line-height: 1.75em;
    margin: 4px auto
}

hr {
    height: 0;
    border: 0;
    border-top: 1px solid #eee
}

.end {
    margin-top: 50px
}

body,
html {
    background-color: #fff;
    font-family: PingFangSC-Light, Nunito, sans-serif;
    height: 100vh;
    margin: 0 8px;
    line-height: 1.75em;
    text-align: left;
}
</style>