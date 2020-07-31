<template>
  <div class="mv-container">
    <div class="mv-wrap">
      <h3 class="title">mv详情</h3>
      <!-- mv -->


      <div class="video-wrap">
        <video
          controls
          :src="mvUrl.url"
        ></video>
      </div>


      <!-- mv信息 -->
      <div class="info-wrap">
        <div class="singer-info">
          <div class="avatar-wrap">
            <!-- 歌手头像 -->
            <img :src="cover" alt="" />
          </div>
          <span class="name">{{mvInfo.artistName}}</span>
        </div>
        <div class="mv-info">
          <h2 class="title">{{mvInfo.name}}</h2>
          <span class="date">发布：xxxx.xx.xx</span>
          <span class="number">播放：{{mvInfo.playCount}}次</span>
          <p class="desc">
            {{mvInfo.desc}}
          </p>
        </div>
      </div>





      <h1 class="comment_title">歌曲评论不做，方法已经在搜索歌曲成功的歌单详情中有，原理相同。<br>api接口数据调用在下方表格</h1>
      <el-table
        :data="tableData"
        stripe
        style="width: 100%;text-align: center;"
        >

          <el-table-column
            prop="date"
            label="备注"
            width="180">
          </el-table-column>

          <el-table-column
            prop="name"
            label="数据"
            width="520">
          </el-table-column>

      </el-table>



    <br><br><br><br><br><br><br><br>









      <!-- 精彩评论 -->
      <div class="comment-wrap">
        <p class="title">精彩评论<span class="number"></span></p>
        <div class="comments-wrap">
          <div class="item">
            <div class="icon-wrap">
              <img src="../assets/avatar.jpg" alt="" />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">爱斯基摩：</span>
                <span class="comment">谁说的，长大了依旧可爱哈</span>
              </div>
              <div class="re-content">
                <span class="name">小苹果：</span>
                <span class="comment">还是小时候比较可爱</span>
              </div>
              <div class="date">2020-02-12 17:26:11</div>
            </div>
          </div>
        </div>
      </div>


      <!-- 最新评论 -->
      <div class="comment-wrap">
        <p class="title">最新评论<span class="number">(666)</span></p>
        <div class="comments-wrap">
          <div class="item">
            <div class="icon-wrap">
              <img src="../assets/avatar.jpg" alt="" />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">爱斯基摩：</span>
                <span class="comment">谁说的，长大了依旧可爱哈</span>
              </div>
              <div class="re-content">
                <span class="name">小苹果：</span>
                <span class="comment">还是小时候比较可爱</span>
              </div>
              <div class="date">2020-02-12 17:26:11</div>
            </div>
          </div>
          <div class="item">
            <div class="icon-wrap">
              <img src="../assets/avatar.jpg" alt="" />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">爱斯基摩：</span>
                <span class="comment">谁说的，长大了依旧可爱哈</span>
              </div>
              <div class="re-content">
                <span class="name">小苹果：</span>
                <span class="comment">还是小时候比较可爱</span>
              </div>
              <div class="date">2020-02-12 17:26:11</div>
            </div>
          </div>
          <div class="item">
            <div class="icon-wrap">
              <img src="../assets/avatar.jpg" alt="" />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">爱斯基摩：</span>
                <span class="comment">谁说的，长大了依旧可爱哈</span>
              </div>
              <div class="re-content">
                <span class="name">小苹果：</span>
                <span class="comment">还是小时候比较可爱</span>
              </div>
              <div class="date">2020-02-12 17:26:11</div>
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
        :limit="limit"
      >
      </el-pagination>


    </div>


    <!-- 相关推荐 -->
    <div class="mv-recommend">
      <h3 class="title">相关推荐</h3>
      <div class="mvs">
        <div class="items">


          <div class="item" v-for="(item,i) in mvs" :key="i">
            <div class="img-wrap">
              <img :src="item.cover" alt="" />
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <div class="num">{{item.playCount}}</div>
              </div>
              <span class="time">{{item.duration}}</span>
            </div>
            <div class="info-wrap">
              <div class="name">{{item.name}}</div>
              <div class="singer">{{item.artistName}}</div>
            </div>
          </div>


        </div>
      </div>
    </div>



  </div>
</template>

<script>

import axios from "axios"

export default {
  name: 'mv',
  data() {
    return {
      // 总条数
      total: 20,
      // 页码
      page: 1,
      // 页容量
      limit: 10,
      mvUrl:[],
      mvs:[],
      mvInfo:{},
      cover:"",//歌手头像

      tableData: [{
          date: '评论接口',
          name: 'https://autumnfish.cn/comment/mv',
        }, {
          date: '必选参数',
          name: 'id：mvid,(传入mv的id)',
        }, {
          date: '可选参数',
          name: 'limit：取出评论数量，默认20',
        },{
          date: '可选参数',
          name: 'offset：偏移数量，用于分页，评论的当前页 = （评论页数-1）* 20，其中20为limit的值',
        }]


    };
  },
  methods: {
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    }
  },

  created(){

    // mv播放地址
    axios({
      url:"https://autumnfish.cn/mv/url",
      params:{
        id:this.$route.query.id
      }
    })
    .then(res=>{
      // console.log(res);
      this.mvUrl = res.data.data
      
    })

    //mv相关推荐
    axios({
      url:"https://autumnfish.cn/simi/mv",
      params:{
        mvid:this.$route.query.id
      }
    })
    .then(res=>{
      // console.log(res);
      this.mvs = res.data.mvs
      
    })

    //mv信息,通过mv的id去调用接口，然后返回信息
    axios({
      url:"https://autumnfish.cn/mv/detail",
      params:{
        mvid:this.$route.query.id
      }
    })
    .then(res=>{
      // console.log(res);
      this.mvInfo = res.data.data

      
      //歌手信息需要在mv信息出来之后才可以获取，
      // 所以这里直接在获取mv信息里嵌套获取歌手信息的方法即可


      //获取歌手信息
        axios({
          url:"https://autumnfish.cn/artists",
          params:{
            id:this.mvInfo.artists[0].id
          }
        })
        .then(res=>{
          console.log(res);
          this.cover = res.data.artist.picUrl
          
        })
      


    })


    




  }



};
</script>

<style scoped>
    .comment_title{
        background-color: #ede;
        padding:20px 10px;
        width: 100%;
        margin-bottom: 15px;
    }

</style>