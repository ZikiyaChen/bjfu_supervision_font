<template>
  <Layout style="height: 100%" class="main">
    <Sider hide-trigger collapsible :width="220" :collapsed-width="64" v-model="collapsed" class="left-sider" :style="{overflow: 'hidden'}">
      <side-menu accordion ref="sideMenu" :active-name="$route.name" :collapsed="collapsed" @on-select="turnToPage" :menu-list="menuList">
        <!-- 需要放在菜单上面的内容，如Logo，写在side-menu标签内部，如下 -->
        <div class="logo-con">
          <div  v-show="!collapsed" >
            <span style="font-size: 21px;padding-left: 20px;color: #eeeeee;display: inline-block">
<!--            <img :src="maxLogo" key="max-logo" style="float: left" />-->
<!--            教发中心业务平台</span></div>-->
            本科教学督导</span>
            <span style="font-size: 21px;padding-left: 20px;color: #eeeeee;display: inline-block">
            评教业务系统</span>
          </div>
<!--          <img v-show="collapsed" :src="minLogo" key="min-logo" />-->
        </div>
      </side-menu>
    </Sider>
    <Layout>
      <Header class="header-con">
        <header-bar :collapsed="collapsed" @on-coll-change="handleCollapsedChange">
          <user :user-avator="userAvator"/>
          <userNotices style="margin-right: 10px;cursor:pointer;"></userNotices>
          <language v-if="$config.useI18n" @on-lang-change="setLocal" style="margin-right: 10px;" :lang="local"/>
          <error-store v-if="$config.plugin['error-store'] && $config.plugin['error-store'].showInHeader" :has-read="hasReadErrorPage" :count="errorCount"></error-store>
          <fullscreen v-model="isFullscreen" style="margin-right: 10px;"/>
          <div style="margin-right: 100px">
<!--            <span style="font-size: small">当前身份：</span>-->
          <ButtonGroup style="margin-top: 14px;float: right"  size="large">
<!--            <Button  type="info">{{current_role}}</Button>-->
<!--            <Button v-for="(value,index) in this.roles" v-if="(value!=='教师')" :key="value"  @click="handleClickToGuider(value)" :type="getBtnType(value)">{{value}}</Button>-->
                <Button v-for="(value,index) in this.roles"  :key="value"  @click="handleClickToGuider(value)" :type="getBtnType(value)">{{value}}</Button>

          </ButtonGroup>
            <span style="margin-right: 30px;font-size: medium" >
              {{this.userInfo.userInfo.name}}老师,您好!
            </span>
          </div>
        </header-bar>
      </Header>
      <Content class="main-content-con">
        <Layout class="main-layout-con">
          <div class="tag-nav-wrapper">
            <tags-nav :value="$route" @input="handleClick" :list="tagNavList" @on-close="handleCloseTag"/>
          </div>
          <Content class="content-wrapper">
              <router-view v-if="show"/>
            <ABackTop :height="100" :bottom="80" :right="50" container=".content-wrapper">
              <div class="top">返回顶端</div>
            </ABackTop>
          </Content>
        </Layout>
      </Content>
    </Layout>
  </Layout>
</template>
<script>
import SideMenu from './components/side-menu'
import HeaderBar from './components/header-bar'
import TagsNav from './components/tags-nav'
import User from './components/user'
import Fullscreen from './components/fullscreen'
import Language from './components/language'
import userNotices from '_c/user_notices'
import ErrorStore from './components/error-store'
import { mapMutations, mapActions, mapGetters } from 'vuex'
import { getNewTagList, getNextRoute, routeEqual } from '@/libs/util'
import minLogo from '@/assets/images/logo.png'
import maxLogo from '@/assets/images/logo.png'
import './main.less'
import UserMixin from '@/mixins/UserMixin'
import ABackTop from '../a-back-top'
export default {
  name: 'Main',
  components: {
    SideMenu,
    HeaderBar,
    Language,
    TagsNav,
    Fullscreen,
    ErrorStore,
    User,
    userNotices,
    ABackTop
  },
  mixins: [UserMixin],
  data () {
    return {
      collapsed: true,
      minLogo,
      maxLogo,
      isFullscreen: false,
      show: true

    }
  },
  computed: {
    ...mapGetters([
      'errorCount'
    ]),
    tagNavList () {
      return this.$store.state.app.tagNavList
    },
    tagRouter () {
      return this.$store.state.app.tagRouter
    },
    userAvator () {
      return this.$store.state.user.avatorImgPath
    },
    cacheList () {
      return this.tagNavList.length ? this.tagNavList.filter(item => !(item.meta && item.meta.notCache)).map(item => item.name) : []
    },
    menuList () {
      return this.$store.getters.menuList
    },
    local () {
      return this.$store.state.app.local
    },
    hasReadErrorPage () {
      return this.$store.state.app.hasReadErrorPage
    }
  },
  methods: {
    ...mapMutations([
      'setBreadCrumb',
      'setTagNavList',
      'addTag',
      'closeTag',
      'setLocal',
      'setCurrentAccess'
    ]),
    ...mapActions([
      'handleLogin'
    ]),
    turnToPage (route) {
      let { name, params, query } = {}
      if (typeof route === 'string') name = route
      else {
        name = route.name
        params = route.params
        query = route.query
      }
      if (name.indexOf('isTurnByHref_') > -1) {
        window.open(name.split('_')[1])
        return
      }
      this.$router.push({
        name,
        params,
        query
      })
    },
    handleCollapsedChange (state) {
      this.collapsed = state
    },
    // handleCloseTag (res, type, route) {
    //   if (type === 'all') {
    //     this.turnToPage(this.$config.homeName)
    //   } else if (routeEqual(this.$route, route)) {
    //     if (type !== 'others') {
    //       const nextRoute = getNextRoute(this.tagNavList, route)
    //       this.$router.push(nextRoute)
    //     }
    //   }
    //   this.setTagNavList(res)
    // },
    handleCloseTag (res, type, route) {
      if (type !== 'others') {
        if (type === 'all') {
          this.turnToPage(this.$config.homeName)
        } else {
          if (routeEqual(this.$route, route)) {
            this.closeTag(route)
          }
        }
      }
      this.setTagNavList(res)
    },
    handleClick (item) {
      this.turnToPage(item)
    },
    handleClickToGuider (value) {
      this.setTagNavList([])
      // 切换用户时将tag清空
      if (value === '督导') {
        this.$router.push({ name: '督导端' })
        this.setCurrentAccess(value)
      } else if (value === '教师') {
        // this.$router.push({name: "督导端"})
        this.$router.push({ name: 'guider_active_attend', query: { time: new Date().getTime() } })
        this.setCurrentAccess(value)
      } else {
        this.$router.push({ name: 'home', query: { time: new Date().getTime() } })
        this.setCurrentAccess(value)
      }
    },
    getBtnType: function (value) {
      if (value === this.current_role) {
        return 'info'
      }
    }
  },
  watch: {
    '$route' (newRoute) {
      const { name, query, params, meta } = newRoute
      this.show = false
      this.$nextTick(() => {
        this.show = true
      })
      this.addTag({
        route: { name, query, params, meta },
        type: 'push'
      })
      this.setBreadCrumb(newRoute)
      this.setTagNavList(getNewTagList(this.tagNavList, newRoute))
      this.$refs.sideMenu.updateOpenName(newRoute.name)
    }
  },
  mounted () {
    /**
     * @description 初始化设置面包屑导航和标签导航
     */
    this.setTagNavList([])
    this.addTag({
      route: this.$store.state.app.homeRoute
    })
    this.setBreadCrumb(this.$route)
    // 设置初始语言
    this.setLocal(this.$i18n.locale)
    // 如果当前打开页面不在标签栏中，跳到homeName页
    if (!this.tagNavList.find(item => item.name === this.$route.name)) {
      // this.$router.push({
      //   name: this.$config.homeName
      // })
    }
  }
}
</script>
<style>
  .top{
    padding: 10px;
    background: rgba(0, 153, 229, .7);
    color: #fff;
    text-align: center;
    border-radius: 2px;
  }
  .start
  {
    cursor: pointer;
  }

  .end
  {
    cursor: pointer;
    color: #fff;
    background: #b1b0b0;
    border: none;
  }
</style>
