<template>
  <div class="player"> 
    <!-- 歌名 歌手名 -->
    <van-cell-group>
      <van-cell>
        <div class="music-name">{{musicName}}</div>
        <div class="singer-name">{{singerName}}</div>
      </van-cell>
   </van-cell-group>

   <!-- 封面 -->
   <van-image
        v-if="musicCover"
        class="sing-image"
        fit="cover"
        :src="musicCover"
    />

    <!-- 评论和MV的开关 没有不显示 -->
    <div>
        <van-icon class="video-o" @click="showPopup1"  v-if="songMV!==null"  name="video-o" size="2rem" />
        <van-icon class="comment-o"  @click="showPopup2"  v-if="userTalk.length!==0 " name="comment-o"  size="2rem" />
    </div>

    <div>
        <audio class="myaudio" ref='audio' @play="play()" @pause="pause()" :src="musicUrl" controls autoplay loop></audio>
    </div>

    <!-- 评论区 -->
    <van-action-sheet v-model="isShow2" title="热门评论">
    <div class="content">
        <van-skeleton  v-for="(item,index) in userTalk " key="index" title avatar :row="6" :loading="loading">
              <van-divider class="user-name" >
                   <div class="avatar-url"><img :src="item.user.avatarUrl" alt="" /></div>
                   {{item.user.nickname}}
              </van-divider>
              <div class="user-content">{{item.content}}</div>
              <van-divider :style="{borderColor: '#fff', padding: '0.3rem 0' }"/>
        </van-skeleton>
    </div>
    </van-action-sheet>
    
    <!-- MV -->
    <van-overlay :show="isShow1" @click="showPopup3">
      <div class="wrapper">
        <div class="block" />
        </div>
      </van-overlay>
      </div>
</template>

<script>

export default {
  data(){
   return {
     musicCover: null,
     musicName:null,
     singerName:null,
     songMV:null,
     userTalk:[],
     loading: true,
     show: false,
     isShow1:false,
     isShow2:false,
     musicUrl: null,
     isPlaying: '',
   }
  },
  methods: {
    getId(){
       this.id =  this.$route.params.id;

      // 歌曲名 歌手 封面
       this.$axios("https://autumnfish.cn/song/detail?ids=" + this.id)
        .then(res =>{
          // console.log(res.data.songs[0].al.picUrl);
          this.musicCover = res.data.songs[0].al.picUrl;
          this.musicName = res.data.songs[0].name;
          this.singerName = res.data.songs[0].ar[0].name;
        })
         .catch(err=>{
           console.log(err);
         }),

       //歌曲评论
       this.$axios("https://autumnfish.cn/comment/hot?type=0&id=" + this.id)
        .then(res =>{
          this.userTalk = res.data.hotComments;
          // console.log(res.data.hotComments);
        })
         .catch(err=>{
           console.log(err);
         })

       //MV
       this.$axios("https://autumnfish.cn/mv/url?type=1004&id=" + this.id)
        .then(res =>{
          this.songMV = res.data.data.url;
          // console.log(this.songMV );
        })
         .catch(err=>{
           console.log(err);
         })

        //歌曲链接
        this.$axios("https://autumnfish.cn/song/url?id=" + this.id)
        .then(res =>{
        //  console.log(res.data);
         this.musicUrl = res.data.data[0].url;
        //  console.log(this.musicUrl);
        })
         .catch(err=>{
           console.log(err);
         })

    },
    showPopup1() {
        this.isShow1 = true;
    },
    showPopup2() {
        this.isShow2 = true;
    },
    showPopup3() {
        this.isShow1 = false;
    },
    showPopup4() {
        this.isShow2 = false;
    },
    play(){
      // console.log("play");
      this.isPlaying = true;
    },
    pause(){
      // console.log("pause");
      this.isPlaying = false;
    },

  
  },

  mounted() {
    this.loading = false;
  },

  created() {
    this.getId()
  }
}
</script>

<style scoped>
.footer{
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
     
}
  img {
    width: 40px;
    height: 40px;
    border-radius: 20px;
  }

  .wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
  }

  .block {
    width: 120px;
    height: 120px;
    background-color: #fff;
  }

  .custom-title {
    margin-left: 14px;
    vertical-align: middle;
  }

  .sing-image {
    width : 10rem;
    height : 10rem;
    margin-top:3rem; 
  }
  
  .music-name{
    font-size: 1.2rem;
    margin: 0.3rem 0;
    color: rgb(43, 43, 43);
  }

  .singer-name{
    color: rgb(133, 133, 133);
  }

  .video-o{
    margin: 1rem;
  }

  .comment-o{
    margin: 1rem;
  }

  .content {
    margin:1rem;
  }

  .content-list{
    margin: 1.5rem 0;
  }

  .avatar-url{
    margin-right:0.8rem ;
  }

  .nick-name{
     float:left;
  }

 .user-name{
   font-size: 1rem;
   margin:0 ;
 }

.user-content{
 font-size: 16px;
 line-height: 1.3rem;
 color: #757575;
}

 .myaudio {
    width: 100%;
    outline: none;
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    background: #f1f3f4;
    vertical-align:bottom;  
    /* 底部消除缝隙 */
 }



</style>