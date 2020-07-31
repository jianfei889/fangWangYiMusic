<template>
  <div class="result-container">



    <div class="title-wrap">
      <h2 class="title">{{searchVal}}</h2>
      <span class="sub-title">已找到{{this.count}}条结果</span>
    </div>



    <!-- 饿了么组件  选项卡标签 -->
    <el-tabs v-model="activeIndex">


      <el-tab-pane label="歌曲" name="songs">
        <table class="el-table">
          <thead>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>

          <tbody >

            <tr class="el-table__row" v-for="(item,index) in musicList" :key="item.id" @click="playMusic(item.id)">
              <td>{{index+1}}</td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <span>{{item.name}}</span>
                    <!-- mv 图标 -->
                    <span v-if="item.mvid!=0" class="iconfont icon-mv"></span>
                  </div>
                  <span v-if="item.alias.length!=0">{{item.alias[0]}}</span>
                </div>
              </td>
              <td>{{item.artists[0].name}}</td>
              <td>{{item.album.name}}</td>
              <td>{{item.duration}}</td>
            </tr>

          </tbody>


        </table>
      </el-tab-pane>



      <el-tab-pane label="歌单" name="lists">
        <div class="items">

          <div class="item" v-for="(item,index) in playList" :key="index" @click="toPlaylist(item.id)">
            <div class="img-wrap">
              <div class="num-wrap">
                播放量:
                <span class="num">{{item.playCount}}</span>
              </div>
              <img :src="item.coverImgUrl" alt="" class="palyCover"/>
              <span class="iconfont icon-play"></span>
            </div>
            <p class="name">{{item.name}}</p>
          </div>
          
        </div>
      </el-tab-pane>


      <el-tab-pane label="MV" name="mv">
        <div class="items mv">


          <div class="item" v-for="item in mvList" :key="item.id">
            <div class="img-wrap">
              <img :src="item.cover" alt="" class="mvCover"/>
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <div class="num">{{item.playCount}}</div>
              </div>
              <span class="time">{{item.duration}}</span>
            </div>
            <div class="info-wrap">
                <!-- MV 名字 -->
              <div class="name">{{item.name}}</div>
                <!-- 歌手名字 -->
              <div class="singer">{{item.artistName}}</div>
            </div>
          </div>
          

        </div>
      </el-tab-pane>




    </el-tabs>



  </div>
</template>

<script>

import axios from 'axios'

export default {
  name: 'result',
  data() {
    return {
      activeIndex: 'songs',
      searchVal:"",//搜索的关键字
      musicList:[],
      count:0,
      playList:[],
      mvList:[]
    };
    
  },

  created(){
    this.searchVal = this.$route.query.query
    
    //获取搜索的关键字
    axios({
      url:"https://autumnfish.cn/search",
      method:"get",
      params:{
        limit:30,
        offset:0,
        type:1,
        keywords:this.searchVal
      }
    })
    .then(res=>{
      // console.log(res);
      this.musicList = res.data.result.songs

      //循环过滤时长
      for(let i=0;i<this.musicList.length;i++){

        let num = Math.floor(this.musicList[i].duration)
        let min = Math.floor(num/1000/60);
        let  s  = Math.floor(num/1000%60);

        if(min<10){
          min = '0'+min
        }
        if(s<10){
          s = '0'+s
        }

        this.musicList[i].duration = min+"："+s

      }

      this.count = res.data.result.songCount

    })



  },

  methods:{
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
    },
    

    toPlaylist(id){
        this.$router.push(`/playlist?q=${id}`)
        
    },


},

  watch:{
    activeIndex(){
        //songs 歌曲
        //list  歌单
        // mv   mv

        let type = 1
        
        switch(this.activeIndex){
          case "songs" :   type = 1;      break;
          case "lists" :   type = 1000;   break;
          case "mv" :   type = 1004;   break;
        }


        //获取搜索的关键字
          axios({
            url:"https://autumnfish.cn/search",
            method:"get",
            params:{
              limit:30,
              offset:0,
              type:type,
              keywords:this.searchVal
            }
          })
          .then(res=>{
            // console.log(res);
              
              //获取歌曲
              if(type==1){
                    this.musicList = res.data.result.songs

                    //循环过滤时长
                    for(let i=0;i<this.musicList.length;i++){

                      let num = Math.floor(this.musicList[i].duration)
                      let min = Math.floor(num/1000/60);
                      let  s  = Math.floor(num/1000%60);

                      if(min<10){
                        min = '0'+min
                      }
                      if(s<10){
                        s = '0'+s
                      }

                      this.musicList[i].duration = min+"："+s

                    }
                    this.count = res.data.result.songCount

              }
              //获取歌单
              else if(type==1000){
                  //歌单逻辑
                  this.playList = res.data.result.playlists
                  this.count = res.data.result.playlistCount


                    //评论数量，过滤
                    for(let i=0;i<this.playList.length;i++){
                      
                      let num = parseInt(this.playList[i].playCount)
                      
                      if(num>10000&&num<100000){
                        // this.playList[i].playCount = (num/10000)+"w+"
                        this.playList[i].playCount = Math.floor(num/10000)+"w+"
                      }
                      if(num>100000){
                        this.playList[i].playCount = 10+"w+"
                      }
                    
                    }


              }

              //获取mv
              else{
                console.log(res);
                
                  
                  this.mvList = res.data.result.mvs
                  this.count = res.data.result.mvCount


                    //评论数量，过滤
                    for(let i=0;i<this.mvList.length;i++){
                      
                      let num = parseInt(this.mvList[i].playCount)
                      
                      if(num>10000&&num<100000){
                        this.mvList[i].playCount = Math.floor(num/10000)+"w+"
                      }
                      if(num>100000){
                        this.mvList[i].playCount = 10+"w+"
                      }
                    
                    }

                    for(let i=0;i<this.mvList.length;i++){
                      let min = parseInt(this.mvList[i].duration/1000/60)
                      let s = parseInt(this.mvList[i].duration/1000%60)
                      if(min<10){
                        min = 0+min
                      }
                      
                      if(s<10){
                        s = 0+s
                      }
                      this.mvList[i].duration = min+":"+s

                    }

 
              }



            
          })


    }
  }




};
</script>

<style scoped>

    .palyCover{
      min-width: 200px;
      min-height: 200px;
    }


</style>
