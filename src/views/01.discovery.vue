<template>
  <div class="discovery-container">

    <!-- 轮播图 -->
    <el-carousel class="" :interval="2000" type="card">
      <el-carousel-item v-for="item in banner" :key="item.targetId">
        <img :src="item.imageUrl" alt="" />
      </el-carousel-item>
    </el-carousel>


    <!-- 推荐歌单 -->
    <div class="recommend">
      <h3 class="title">
        推荐歌单
      </h3>
      <div class="items">

        <div class="item" v-for="item in discoverlist" :key="item.id">
          <div class="img-wrap">
            <div class="desc-wrap">
              <span class="desc">{{item.copywriter}}</span>
            </div>
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play"></span>
          </div>
          <p class="name">{{item.name}}</p>
        </div>
        
      </div>
    </div>


    <!-- 最新音乐 -->
    <div class="news">
      <h3 class="title">
        最新音乐
      </h3>
      <div class="items">


        <div class="item" v-for="item in newMusicList" :key="item.id">
          <div class="img-wrap">
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play" @click="playMusic(item.id)"></span>
          </div>
          <div class="song-wrap">
            <div class="song-name">{{item.song.name}}</div>
            <div class="singer">{{item.song.artists[0].name}}</div>
          </div>
        </div>
       
      </div>
    </div>


    <!-- 推荐MV -->
    <div class="mvs">
      <h3 class="title">推荐MV</h3>
      <div class="items">


        <div class="item" v-for="item in newMvList" :key="item.id">
          <div class="img-wrap">
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play"></span>
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <div class="num">{{item.palyCount}}</div>
            </div>
          </div>
          <div class="info-wrap">
            <div class="name">{{item.name}}</div>
            <div class="singer">{{item.artistName}}</div>
          </div>
        </div>
      
      </div>
    </div>



  </div>
</template>

<script>

  import axios from 'axios'

export default {
  name: 'discovery',
  data(){
    return{
        banner:[],
        discoverlist:[],//推荐歌单
        newMusicList:[],
        newMvList:[]
    }
  },

  
  //html页面渲染之前执行
  created(){

      //轮播图数据
          axios({
            url:"https://autumnfish.cn/banner",
            method:"get"
          })
          .then((res)=>{
              this.banner = res.data.banners
              // console.log(res);
              
              
          })

      //推荐歌单
          axios({
            url:"https://autumnfish.cn/personalized",
            method:"get",
            params:{
              limit:10
            }
          })
          .then(res=>{
              this.discoverlist = res.data.result
              // console.log(res);
          })

      //最新音乐
          axios({
            url:"https://autumnfish.cn/personalized/newsong",
            method:'get'
          })
          .then(res=>{
            this.newMusicList = res.data.result
            // console.log(res.data.result);
            
          })

      //最新MV
          axios({
            url:"https://autumnfish.cn/personalized/mv"
            // method:"get"
          })
          .then(res=>{
            // console.log(res.data.result);
            this.newMvList = res.data.result
          })

    
  },

  methods:{
    //播放音乐，点击最新音乐的播放按钮时播放音乐
      playMusic(id){
          axios({
            url:" https://autumnfish.cn/song/url",
            method:"get",
            params:{
              id
            }
          })
          .then(res=>{
            // console.log(res.data.data[0].url);
            let url = res.data.data[0].url
            // console.log(this.$parent.musicUrl);//$parent,拿取父组件的数据
            this.$parent.musicUrl = url
          })
      }
  }


};


</script>

<style >

</style>
