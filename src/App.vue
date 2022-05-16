<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import Translation from './components/Translation.vue'
</script>

<template>
  <div class="coverPage">
    <h1>论文翻译去重</h1>
  </div>
  <Translation />
  <footer class="footer">
    <blockquote class="text-right">
      <small>——仅做学习交流，禁止用作商业用途<cite title="Source Title"> <a target="_blank"
            href="http://fangyuan99.xyz">&copy;fangyuan99</a></cite></small>
    </blockquote>
  </footer>
</template>

<style>
.coverPage {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

body {
  background-color: #f5f5f5;
}

.footer {
  color: #8D99A6;
  font-size: 0.875rem;
  padding-top: 1rem;
  padding-bottom: 1rem;
  text-align: right;
}
</style>
