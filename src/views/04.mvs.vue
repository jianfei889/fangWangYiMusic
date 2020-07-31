<template>
  <div class="mvs-container">


    <div class="filter-wrap">
      <div class="seciton-wrap">
        <span class="section-type">地区:</span>
        <ul class="tabs-wrap">
          <li class="tab">
            <span class="title" :class="{active:area=='全部'}" @click="area='全部' ">全部</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:area=='内地'}" @click="area='内地' ">内地</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:area=='港台'}" @click="area='港台' ">港台</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:area=='欧美'}" @click="area='欧美' ">欧美</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:area=='日本'}" @click="area='日本' ">日本</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:area=='韩国'}" @click="area='韩国' ">韩国</span>
          </li>
        </ul>
      </div>


      <div class="type-wrap">
        <span class="type-type">类型:</span>
        <ul class="tabs-wrap">
          <li class="tab">
            <span class="title" :class="{active:type=='全部'}" @click="type='全部' ">全部</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:type=='官方版'}" @click="type='官方版' ">官方版</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:type=='原声'}" @click="type='原声' ">原声</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:type=='现场版'}" @click="type='现场版' ">现场版</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:type=='网易出品'}" @click="type='网易出品' ">网易出品</span>
          </li>
        </ul>
      </div>

      <div class="order-wrap">
        <span class="order-type">排序:</span>
        <ul class="tabs-wrap">
          <li class="tab">
            <span class="title" :class="{active:order=='上升最快'}" @click="order='上升最快' ">上升最快</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:order=='最热'}" @click="order='最热' ">最热</span>
          </li>
          <li class="tab">
            <span class="title" :class="{active:order=='最新'}" @click="order='最新' ">最新</span>
          </li>
        </ul>
      </div>
    </div>


    <!-- mv容器 -->
    <!-- 推荐MV -->
    <div class="mvs">
      <div class="items">


        <div class="item" @click="toMv(item.id)" v-for="(item,index) in list" :key="index"  > <!--@click="toMv(10)" -->
          <div class="img-wrap">
            <img :src="item.cover" alt="" />
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <div class="num playCount">{{item.playCount}}</div>
            </div>
          </div>
          <div class="info-wrap">
            <div class="name">{{item.name}}</div>
            <div class="singer">{{item.artistName}}</div>
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


    
  <!-- </div> -->
</template>

<script>

  import axios from "axios"


export default {
  name: 'mvs',
  data() {
    return {
      list:[],//mv列表
      // 总条数
      total: 20,
      // 页码
      page: 1,
      // 页容量
      limit: 8,
      area:'全部',//地区
      type:"全部",//类型
      order:"上升最快"//排序
    };
  },
  methods: {
    toMv(id) {
      this.$router.push(`/mv?id=${id}`);
      
    },
    handleCurrentChange(val) {
      //分页步骤，保存页码，重新发送数据
      this.page = val
      this.getList()
    },

     getList(){
        //获取音乐mv的数据
          axios({
            url:"https://autumnfish.cn/mv/all",
            method:"get",
            params:{
              area:this.area,//地区
              type:this.type,//类型
              order:this.order,//排序

              limit:this.limit,//页容量
              offset:(this.page-1)*this.limit,//偏移值，分页，（页码-1）*数量
              total:this.total,//总条数
              page:this.page//页码
                
            }
          })
          .then(res=>{
            // console.log(res);
            this.list = res.data.data//mv列表

            //评论数量，过滤
            for(let i=0;i<this.list.length;i++){
              
              let num = parseInt(this.list[i].playCount)
              
              if(num>10000&&num<100000){
                // this.list[i].playCount = (num/10000)+"w+"
                this.list[i].playCount = Math.floor(num/10000)+"w+"
              }
              if(num>100000){
                this.list[i].playCount = 10+"w+"
              }
            
            }

            //保存总条数
            this.totla = res.data.count


          })
    }


  },

  created(){
      this.getList()
  },

  watch:{
    area(){   this.page=1, this.getList() },
    type(){  this.page=1, this.getList() },
    order(){  this.page=1, this.getList() },
  }


};
</script>

<style scoped>
    .active{
      color:#ff6700;
    }

    .playCount{
      color: #000;
      background-color: #fff;
      opacity: 0.8;
    }
</style>
