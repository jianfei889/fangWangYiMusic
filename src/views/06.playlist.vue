<template>
  <div class="playlist-container">


    <div class="top-wrap">
      <div class="img-wrap">
        <!-- 封面 -->
        <img :src="playlist.coverImgUrl" alt="" />
      </div>
      <div class="info-wrap">
        <!-- 歌名标题 -->
        <p class="title">{{playlist.name}}</p>
        <div class="author-wrap">
          <!-- 头像 -->
          <img class="avatar" :src="playlist.creator.avatarUrl" alt="" />
          <span class="name">{{playlist.creator.nickname}}</span>
          <span class="time">{{playlist.createTime}} 创建</span>
        </div>
        <div class="play-wrap">
          <span class="iconfont icon-circle-play"></span>
          <span class="text">播放全部</span>
        </div>
        <div class="tag-wrap">
          <span class="title">标签:</span>
          <ul>
            <li v-for="(item,index) in playlist.tags" :key="index">{{item}}</li>
          </ul>
        </div>
        <div class="desc-wrap">
          <span class="title">简介:</span>
          <span class="desc">
            <!-- 简介 -->
            {{playlist.description}}  
          </span
          >
        </div>
      </div>
    </div>


    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲列表" name="1">
        <table class="el-table playlit-table">
          <thead>
            <th></th>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>
          <tbody>

            
            <tr class="el-table__row" v-for="item in playlist.tracks" :key="item" @click="playMusic(item.id)">
              <td>1</td>
              <td>
                <div class="img-wrap">
                  <img :src="item.al.picUrl" alt="" />
                  <span class="iconfont icon-play" ></span>
                </div>
              </td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <span>{{item.al.name}}</span>
                    <span class="iconfont icon-mv"></span>
                  </div>

                  <span>公司</span>
                </div>
              </td>
              <td>{{item.ar[0].name}}</td>
              <td>{{item.name}}</td>
              <td>{{item.dt}}</td>
            </tr>


          </tbody>
        </table>
      </el-tab-pane>





      <el-tab-pane label="评论 name="2">
        <!-- 精彩评论 -->
        <div class="comment-wrap" >
          <p class="title">精彩评论<span class="number">({{hotCount}})</span></p>


          <div class="comments-wrap" v-for="(item,i) in hotComment" :key="i">
            <div class="item">
              <div class="icon-wrap">
                  <!-- 头像 -->
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{item.user.nickname}}：</span>
                  <span class="comment">{{item.content}}</span>
                </div>

                <!-- 评论回复 -->
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{item.beReplied[0].user.nickname}}：</span>
                  <span class="comment">{{item.beReplied[0].content}}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
              </div>
            </div>
          </div>


        </div>


        <!-- 最新评论 -->
        <div class="comment-wrap">
          <p class="title">最新评论<span class="number">({{total}})</span></p>
          <div class="comments-wrap">

            <div class="item" v-for="(item,index) in comments" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{item.user.nickname}}：</span>
                  <span class="comment">{{item.content}}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{item.beReplied[0].user.nickname}}：</span>
                  <span class="comment">{{item.beReplied[0].content}}</span>
                </div>
                <!-- 时间戳： -->
                <div class="date">{{item.time}}</div>
              </div>
            </div>
            

          </div>
        </div>

        <!-- 分页器 -->
        <el-pagination
          @current-change="handleCurrentChange"
          background
          layout="prev, pager, next"
          :total="total"
          :current-page="page"
          :page-size="5"
        >
        </el-pagination>
      </el-tab-pane>
    </el-tabs>


  </div>
</template>

<script>

import axios from 'axios'

export default {
  name: 'playlist',
  data() {
    return {
      activeIndex: '1',
      // 总条数
      total: 0,
      // 页码
      page: 1,
      playlist:[],
      hotComment:[],
      hotCount:0,//热门评论个数
      comments:[]//普通评论
    };
  },
  methods: {
    handleCurrentChange(val) {
        this.page = val

              axios({
              url:"https://autumnfish.cn/comment/playlist",
              method:"get",
              params:{
                  id:this.$route.query.q,
                  limit:10,
                  //根据页码计算
                  offset:(this.page-1)*10
              }
            })
            .then(res=>{
                  
                  this.total = res.data.total
                  this.comments = res.data.comments

            })

    },

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


  },

  created(){

    //获取歌单
      axios({
        url:"https://autumnfish.cn/playlist/detail",
        method:"get",
        params:{
            id:this.$route.query.q
        }
      })
      .then(res=>{
        // console.log(res);
        this.playlist = res.data.playlist
        let times = res.data.playlist.createTime 
        
        

        for(let i=0;i<this.playlist.tracks.length;i++){
            let duration = this.playlist.tracks[i].dt
            //假定 duration = 600000----- == 一分钟 == 60秒
            //测试：  min = 60000/1000/60     总时长除以1000等于总秒数，总秒数除以60等于分钟数
            //        s = 600000/1000%60      总秒数磨60之后剩余的秒数就是有效的时长秒数
            let min = parseInt(duration/1000/60)
            let s = parseInt(duration/1000%60)

            if(min<10){
              min='0'+min
            }
            
            if(s<10){
              s='0'+s
            }

            this.playlist.tracks[i].dt = `${min}:${s}`
          
        }
        


        
      })

    //获取评论--热门评论
      axios({
        url:"https://autumnfish.cn/comment/hot",
        method:"get",
        params:{
            id:this.$route.query.q,
            type:2
        }
      })
      .then(res=>{
            // console.log(res);
            this.hotComment = res.data.hotComments
            this.hotCount = res.data.total
      })


    //获取最新评论
      axios({
        url:"https://autumnfish.cn/comment/playlist",
        method:"get",
        params:{
            id:this.$route.query.q,
            limit:10,
            offset:0
        }
      })
      .then(res=>{
            // console.log(res);
            
            this.total = res.data.total
            this.comments = res.data.comments


      })



  }


















};
</script>

<style >

</style>
