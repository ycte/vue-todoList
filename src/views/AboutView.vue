<script setup>
import { ref } from 'vue'
import axios from 'axios'
import EmailIcon from './icons/IconEmail.vue'
import GithubIcon from './icons/IconGithub.vue'

const userInfo = ref({
  name: 'ycte',
  avatar: 'https://avatars.githubusercontent.com/u/18048702?v=4',
  url: 'https://github.com/ycte',
  email: 'ycte@163.com'
})

// 获取 github 用户 ycte 的信息
const url = 'https://api.github.com/users/ycte'

axios.get(url).then((response) => {
  console.log("个人信息", response.data)
  userInfo.value.name = response.data.login
  userInfo.value.avatar = response.data.avatar_url
  userInfo.value.url = response.data.html_url
  if (response.data.email) {
    userInfo.value.email = response.data.email
  }
})
</script>


<template>
  <div class="about">
    <img :src="userInfo.avatar" alt="avatar" width="100" height="100" />
    <!-- 这里不能使用 v-html，因为 v-html 会将 HTML 代码渲染成 DOM 结构，而不是文本 -->
    <br />
    <h1>{{ userInfo.name }}</h1>
    <span class="social-icon"><EmailIcon /></span>
    <span>{{ userInfo.email }}</span>
    <br />
    <span class="social-icon">
      <a :href="userInfo.url" target="_blank">
        <GithubIcon />
      </a>
    </span>
    <span>
      <a :href="userInfo.url">{{ userInfo.url }}</a>
    </span>
    <br />
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
.social-icon {
  margin-top: 3%;
  margin-bottom: 3%;
  margin-right: 2%;
}
</style>
