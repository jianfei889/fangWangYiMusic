<template>
  <div class="songs-container">
    <div class="tab-bar">
      <span class="item" :class="{active:tag==0}"  @click="tag=0">全部</span>
      <span class="item" :class="{active:tag==7}"  @click="tag=7">华语</span>
      <span class="item" :class="{active:tag==96}" @click="tag=96">欧美</span>
      <span class="item" :class="{active:tag==8}"  @click="tag=8">日本</span>
      <span class="item" :class="{active:tag==16}" @click="tag=16">韩国</span>
    </div>


    <!-- 底部的table -->
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


        <!-- v-for时，如果写上的是 (item,index) 包含index索引，建议使用index，（定义了就要使用的意思） -->

        <tr class="el-table__row" v-for=" (item,index) in lists" :key="index">
          <td>{{index+1}}</td>
          <td>
            <div class="img-wrap">
              <img :src="item.album.picUrl" alt="" />
              <span class="iconfont icon-play" @click="playMusic(item.id)"></span>
            </div>
          </td>
          <td>
            <div class="song-wrap">
              <div class="name-wrap">
                <span>{{item.name}}</span>
                <span class="iconfont icon-mv"></span>
              </div>
              <span>{{item.album.company}}</span>
            </div>
          </td>
          <td>{{item.album.artists[0].name}}</td>
          <td>{{item.album.name}}</td>
          <td>{{item.duration}}</td>
        </tr>
       
      </tbody>
    </table>



  </div>
</template>

<script>

  import axios from "axios"

export default {
  name: 'songs',
  data() {
    return {
          lists:[],
          tag:0,
          musicUrl:""
    };
  },

  created(){
      //获取音乐数据
      this.getList()

  },

  watch:{
    tag(){
        //获取音乐数据
        this.getList()
    }
  },

  methods:{
    //获取音乐数据
    getList(){
      axios({//获取音乐数据
        url:"https://autumnfish.cn/top/song",
        methid:"get",
        params:{
          type:this.tag
        }
      })
      .then(res=>{
        // console.log(res);
        
        this.lists = res.data.data


        //处理时长，从毫秒转为分、秒
        for(let i=0;i<this.lists.length;i++){
          let duration = this.lists[i].duration
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

          // this.lists[i].duration = min+":"+s
          this.lists[i].duration = `${min}:${s}`
          
        }

 
      })
  },

  //播放音乐
    playMusic(id){
      axios({
        url:"https://autumnfish.cn/song/url",
        params:{
          id
        }
      })
      .then(res=>{
          // console.log(res);
          let url = res.data.data[0].url
          this.$parent.musicUrl = url
          
      })

    }



  }




};
</script>

<style  scoped>
    .active{
      color: #ff6700;
    }

</style>>