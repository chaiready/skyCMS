<template>
  <div class="warp" >
    <template>
      <div class="breadcrumb">
        <a-breadcrumb :routes="routes">
          <template slot="itemRender" slot-scope="{route, params, routes, paths}">
            <span v-if="routes.indexOf(route) === routes.length - 1">
              {{route.breadcrumbName}}
            </span>
            <router-link v-else :to="`/${paths.join('/')}`">
              {{route.breadcrumbName}}
            </router-link>
          </template>
        </a-breadcrumb>
      </div>
    </template>
    <a-divider />
    <section>
      <h1 class="title" :style="{color:artData.titlecolor}">{{artData.title}}</h1>
    </section>
    <section class="artDescribe">
      <a-row type="flex" justify="center" :gutter="24">
        <a-col><a-icon type="clock-circle" /> {{artData.creat_time}}</a-col>
        <a-col><a-icon type="schedule" /> {{artData.cate_name}}</a-col>
        <a-col><a-icon type="fire" /> {{artData.views}}</a-col>
      </a-row>
    </section>
    <div class="article ql-snow" >
      <article class="ql-editor" v-html="artData.content"></article>
    </div>
    <div class="like">
      <a class="like-warp" @click="like" style=""><a-icon type="like" /></a>
      <span>{{artData.like}}</span>
    </div>
  </div>
</template>

<script>
export default {
  asyncData({ store, route }) {
    let routerParams = route.params;
    if (routerParams.article) {
      return store.dispatch("articleDetail", { id: routerParams.article });
    }
  },
  data(){
    return {
      artData:this.$store.state.artDetail,
      routes: [
        {
          path: '/',
          breadcrumbName: '首页',
        },
        {
          path: this.$store.state.artDetail.cate_path || '',
          breadcrumbName: this.$store.state.artDetail.cate_name || '',
        },
        {
          breadcrumbName: this.$store.state.artDetail.title ||'',
        },
      ],
    }
  },
  mounted(){
    let title = this.$route.meta.title;
    document.title = this.routes[2].breadcrumbName+'-'+title;
  },
  methods:{
    async like(){
      let ret = await this.$axios.post('artical/addLick',{
        'id':this.artData._id
      });
      this.$message.config({
        top: `15%`,
        duration: 2,
        maxCount: 3,
      });
      if(ret.code==200){
        this.artData.like = this.artData.like+1;
        this.$message.success('感谢您的赞美！');
      }else if(ret.code==201){
        this.$message.warning('过多的赞美，会使我膨胀的哦！');
      }else{
        this.$message.error('理解您想赞美我的心情，但是现在系统出错啦，请待会儿重试哦！');
      }
    }
  }
}
</script>


<style>
/*   .warp img{
    max-width: 100%;
    vertical-align:bottom;
  } */
  .warp .article{
    margin-top: 20px;
  }
  .warp .title{
    text-align: center;
  }
  .warp .artDescribe i{
    vertical-align: middle;
  }
  .warp>article{
    padding: 10px;
  }
  .warp .like{
    text-align: center;
    font-size: 33px;
    margin-top:40px; 
  }
  .warp .like span{
    display: block;
    color:#999;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'Helvetica Neue', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';
    font-variant: tabular-nums;
    line-height: 1.5;
  }
  .warp .like a{
    color: #fff;
    display: block;
    width: 64px;
    height: 64px;
    line-height: 64px;
    background: #409eff;
    margin: 0 auto;
    border-radius: 50%;
  }
</style>