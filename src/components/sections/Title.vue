<script lang="ts" setup>

import { Files, Film } from '@element-plus/icons-vue'

const title = 'POEX: Demystifying Policy Executable Jailbreaks against LLM-based Robots'

const title_color = '#000000'


const btn_color = '#444444'


const authors = [
  {
    name: "Anonymous Authors",
  },
]


const buttons = [
  {
    disabled: false,
    name: "Datasets",
    link: "https://github.com/usenix-poex/Harmful-RLbench",
    component: Files,
  },
  {
    disabled: false,
    name: "Code",
    link: "https://github.com/usenix-poex/POEX",
    component: Files,
  },
  {
    disabled: false,
    name: "Video",
    link: "https://www.youtube.com/channel/UCeEyVsTXJV9hR82PjVVo83Q",
    component: Film,
  },
]

</script>

<template>
  <div>

    <el-row justify="center">
      <el-col :span="24">
        <el-alert title="🔥 Welcome to POEX homepage!" type="success" />
      </el-col>
    </el-row>

    <el-row justify="center">
      <el-col :span="20">
        <h1 class="paper-title">
          <span :style="{color:title_color}">{{ title }}</span>
        </h1>
      </el-col>
    </el-row>

    <el-row justify="center">
      <span v-for="author in authors">
        <el-button class="title-button" type="primary" text>
          <span class="author">{{ author.name }}</span>
        </el-button>
      </span>
    </el-row>

    <el-row justify="center" style="margin-bottom: 20px;">
      <el-col :span="20">
        <el-row justify="center">
          <a :href=button.link v-for="button in buttons">
            <el-button class="guidance-button" size="default" :color="btn_color" :disabled="button.disabled" round>
              <el-icon :size="18">
                <component :is="button.component" />
              </el-icon>
              <span class="btn-text">{{ button.name }}</span>
            </el-button>
          </a>
        </el-row>
      </el-col>
    </el-row>

  </div>
</template>

<style scoped>

.paper-title {
  font-family: "MyFont", Verdana, sans-serif;
  letter-spacing: 2px;
  font-size: 42px;
  margin: 32px;
  text-align: center;
}

.title-button {
  margin: 10px 3px;
}

.title-button:hover {
  margin: 10px 8px;
}

.guidance-button {
  margin: 8px 5px;
  box-shadow: #d8d8d8 1px 1px 1px 1px;
}

.author {
  font-size: 18px;
  margin-left: 3px;
}

.btn-text {
  font-size: 18px;
  color: #ffffff;
}

.el-alert {
  margin: 10px 0 0;
}

.el-alert:first-child {
  margin: 0;
}

a {
  text-decoration: none;
  color: inherit;
}

a:hover {
  color: inherit;
  border-bottom: none;
}

</style>