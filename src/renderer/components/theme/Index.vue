<template>
  <div class="">
    <v-card flat>
      <v-card-title>
        <span class="headline">🌁 主 题</span>
      </v-card-title>
      <v-container fluid>
        <v-form>
          <v-select v-model="form.themeName" :items="site.themes" label="选择主题" outline />
          <v-text-field label="网站名称" v-model="form.siteName" />
          <v-text-field label="网站描述" v-model="form.siteDescription" />
          <v-text-field label="底部信息" v-model="form.footerInfo" />
          <v-switch label="是否显示文章大图" v-model="form.showFeatureImage" />
          <v-slider label="每页文章数" v-model="form.pageSize" thumb-label="always" :min="1" :max="20" always-dirty />
          <v-subheader class="pl-0">
            社交链接 
            <v-btn fab flat dark small color="primary">
              <v-icon dark>add</v-icon>
            </v-btn>
          </v-subheader>
          <v-chip close>
            <v-avatar>
              <v-icon>movie</v-icon>
            </v-avatar>
            知乎
          </v-chip>
          <div>
            <v-btn depressed color="primary" @click="saveTheme">保 存</v-btn>
          </div>
        </v-form>
      </v-container>
    </v-card>
  </div>
</template>

<script lang="ts">
import { ipcRenderer, Event } from 'electron'
import Vue from 'vue'
import Component from 'vue-class-component'
import { State } from 'vuex-class'
import { Site } from '../../store/modules/site'

@Component
export default class Theme extends Vue {
  @State('site') site!: Site

  form = {
    themeName: '',
    pageSize: 10,
    siteName: '',
    siteDescription: '',
    footerInfo: '',
    showFeatureImage: true,
  }

  saveTheme() {
    ipcRenderer.send('theme-save', this.form)
    ipcRenderer.once('theme-saved', (event: Event, result: any) => {
      this.$bus.$emit('site-reload')
      this.$bus.$emit('snackbar-display', '主题已保存')
    })
  }
  
  mounted() {
    console.log('主题', this.site)
    this.form = this.site.themeConfig
  }
}
</script>

<style scoped>
</style>
