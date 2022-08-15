<template>
  <div id="app">
    <el-container>
      <el-header>
        <el-menu :default-active="activeIndex" mode="horizontal" >
          <el-menu-item index="1" @click="activeIndex='2'"><a href="https://www.freejishu.com" target="_blank">freejishu的美丽世界</a></el-menu-item>
          <el-menu-item index="2" @click="activeIndex='2'">音乐混合器</el-menu-item>
          <el-menu-item index="3" @click="activeIndex='3'">使用说明</el-menu-item>
          
        </el-menu>
      </el-header>
      <el-main>
        <el-col style="padding:5vh 10vw">
          <div v-if="activeIndex=='2'">
          
          <h2>播放</h2>
          <br />


          <center>
            <el-row  class="row-bg" justify="space-around">
              <el-col :xs="24" :sm="24" :md="12" :lg="12" :xl="12">
                <el-card class="music-card">
                  {{now_music[0]}}<br /><br />
                  <audio ref="audio" :src="music_link1" controls preload="auto" id="player1" @seeked="test_sync_time" @ended="play_ended"></audio>
                </el-card>
              </el-col>

              <el-col :xs="24" :sm="24" :md="12" :lg="12" :xl="12">
                <el-card class="music-card">
                  {{now_music[1]}}<br /><br />
                  <audio ref="audio" :src="music_link2" controls preload="auto" id="player2" @seeked="test_sync_time" @ended="play_ended"></audio>
                </el-card>
              </el-col>
            </el-row>
          </center>
          

          
          <br />
          <h2>控制区</h2>

          <el-dropdown @command="change_music" placement="top-start" trigger="hover">
            <el-button type="primary">
              {{this.button_name}}<i class="el-icon-arrow-down el-icon--right"></i>
            </el-button>
            <el-dropdown-menu slot="dropdown" >
              <el-dropdown-item v-for="(music, index) in music_group" :key="index"  :command="index">{{music[0]+' & '+music[1]}}</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
          &nbsp;<br /><br />
          <el-button type="primary" @click="play_music" :disabled="!music_group_select">开始播放</el-button>
          
          <el-tooltip class="item" effect="dark" content="如果播放进度出现偏移，可重复点击此按钮进行进度对齐" placement="bottom">
            <el-button type="primary" plain @click="sync_time" :disabled="now_player_com == 0" >对齐进度</el-button>
          </el-tooltip>
          <el-button type="primary" plain @click="get_time" :disabled="now_player_com == 0" v-if="dev_mode">获取进度</el-button>
          &nbsp;
          <el-button-group >
            <el-tooltip class="item" effect="dark" content="歌曲1-100%" placement="bottom">
              <el-button type="primary" @click="change_song(1)" :disabled="now_player_com == 0 || now_player_com == 1">1</el-button>
            </el-tooltip>
            <el-tooltip class="item" effect="dark" content="歌曲2-100%" placement="bottom">
              <el-button type="primary" @click="change_song(2)" :disabled="now_player_com == 0 || now_player_com == 2">2</el-button>
            </el-tooltip>
            <el-tooltip class="item" effect="dark" content="歌曲1-75%/歌曲2-75%" placement="bottom">
              <el-button type="primary" @click="change_song(3)" :disabled="now_player_com == 0 || now_player_com == 3">1+2</el-button>
            </el-tooltip>
          </el-button-group>
          &nbsp;
          <el-button type="primary" plain @click="show_setting ? show_setting =  false : show_setting = true" >{{show_setting ? '收起设置' : '更多设置'}}</el-button>


          <br /><br />
          <transition name="el-fade-in-linear">
            <el-card class="box-card" v-if="show_setting">
              <el-row :gutter="5">
              <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
                  <div>
                    <span class="setting-size">循环播放&nbsp;&nbsp;
                    <el-switch v-model="loop_play">
                    </el-switch>
                    &nbsp;</span>
                  </div>
                </el-col>
                <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
                  <div>
                    <span class="setting-size">切换过渡时长&nbsp;
                    <el-input-number v-model="mix_time_sum" :step="100" :max="1000" :min="100" size="small" @change="change_mix_time"></el-input-number>
                    &nbsp;ms</span>
                  </div>
                </el-col>
                <!--
                <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
                  <div>
                    <span class="setting-size">&nbsp;
                    <el-input-number v-model="mix_times_s" :step="100" size="small"></el-input-number>
                    &nbsp;ms</span>
                  </div>
                </el-col>
                <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
                  <div>
                    <span class="setting-size">音量切换&nbsp;
                    <el-input-number v-model="mix_times_s" :step="100" size="small"></el-input-number>
                    &nbsp;ms</span>
                  </div>
                </el-col>
                -->
                <el-col :xs="4" :sm="6" :md="8" :lg="9" :xl="11"><div class="grid-content bg-purple"></div></el-col>
                <el-col :xs="8" :sm="6" :md="4" :lg="3" :xl="1"><div class="grid-content bg-purple-light"></div></el-col>
              </el-row>
              

              
            </el-card>
          </transition>
          <br />
          <br />
          


          </div>
          <div v-if="activeIndex=='3'">
            <h1>使用说明</h1>

            <h3>这是一个什么东西？</h3>

            <p>音乐混合器，说白了是可以把两首音乐进行同步播放的工具。</p>
            <p>这块工具设计的初衷和灵感，来自原神的音乐“无缝切换”，即背景音乐和战斗音乐可根据角色当前状态自动切换并同步相同的进度。截至2.8版本，这种设计出现在了雷神周本和层岩巨渊的地心处。可预见的，后期可能会有更多这样的音乐支持此玩法，故写了这么一个音乐混合器。</p>

            <h3>如何操作？</h3>

            <p>无需操作上方的播放控制，请使用下方<b>控制区</b>内的按钮。先<b>选择音乐组……</b>，再<b>开始播放</b>；</p>
            <a href="https://img.freejishu.com/image/APsu"><img src="https://cdn-p.freejishu.com/img/2022/08/15/APsu.png" alt="APsu.png" border="0" style="width:100%"/></a>
            <p>播放过程中可随时使用<b>1（对应键盘数字1）</b>、<b>2（对应键盘数字2）</b>、<b>1+2（对应键盘数字3）</b>进行无缝切换混合。</p>
            <p>若播放过程中出现卡顿等情况，可使用<b>对齐进度</b>按钮进行进度自动对齐。</p>
            <p><b>更多设置</b>内含有部分高级设置，可自行选用。</p>
            <p>注：本页面上所有音乐来源于网易云音乐，本页面仅作引用。</p>


          </div>
        </el-col>
      </el-main>
    </el-container>
    <!--https://music.163.com/#/song?id=-->
  </div>
</template>

<script>
//import HelloWorld from './components/HelloWorld.vue'



export default {
  name: 'App',
  data() {
      return {
        activeIndex: '3',
        activeName: 'second',
        button_name: '选择音乐组……',
        music_link1: '',
        music_link2: '',
        music_group: [
          ['Stories of Remote Antiquity 深埋地心的瑰秘','Inevitable Conflict 激扬的韧战','1956543019','1956543025',160],
          ['Thunderings of the Merciless 雷霆的威光','The Almighty Violet Thunder 稻光神鸣','1937111535','1937111527',-120],
          ['The Realm of Tokoyo 常世之国','Hope or Nostalgia 祈望与乡魂','1937114376','1937114373',0,0],
        ],
        music_group_select : false,
        now_music: ['Music1','Music2'],
        now_player_com: 0,
        play_status: 0,//0 noplay 1 playing 2mixing
        mix_times: 10,
        mix_times_s: 100,
        mix_time_sum: 1000,
        dev_mode: false,
        show_setting: false,
        loop_play: true,
      };
    },
  mounted(){
    //this.loadData()
    var _this = this;        
    // eslint-disable-next-line
    document.onkeydown = function(e) {            
      let key = window.event.keyCode;         
      console.log(1234,e,key,_this.play_status) ;
      if (_this.play_status == 1) {
        console.log(11111)
        if (key== 49 || key== 97) {
          console.log(123456) 
          _this.change_song(1);
        }else if (key== 50 || key== 98) {
          console.log(2222) 
          _this.change_song(2);
        }else if (key== 51 || key== 99) {
          console.log(3333) 
          _this.change_song(3);
        }
      }  
      
    };
  },
  methods: {
      goBack() {
        console.log('go back');
      },
      change_music(index){
        console.log(index);
        this.button_name = this.music_group[index][0]+ ' & '+this.music_group[index][1];
        this.now_music = this.music_group[index];
        this.music_link1 = "http://music.163.com/song/media/outer/url?id="+this.music_group[index][2];
        this.music_link2 = "http://music.163.com/song/media/outer/url?id="+this.music_group[index][3];
        this.music_group_select = true;
      },
      play_music(type){
        var audio1 = document.getElementById("player1");
        var audio2 = document.getElementById("player2");
        let start_time_1 = 0;
        let start_time_2 = 0;
        let sync_tag = 0;
        var self = this;
        if(this.now_music[4] < 0){
          start_time_1 = 50+self.now_music[4]-(2*self.now_music[4]);
          start_time_2 = 50;//500+self.now_music[4]; 0
          sync_tag = 1;

        }else{
          start_time_1 = 50;
          start_time_2 = 50+self.now_music[4];
          sync_tag = 2;
        }
        console.log(sync_tag);
        
        setTimeout(function () {
            audio1.play();
            if(type != 1){
              audio1.volume = 0.75;
            }
            
            if ( sync_tag == 1) self.sync_time();
        }, start_time_1);

        setTimeout(function () {
            audio2.play();
            if(type != 1){
              audio2.volume = 0;
            }
            if ( sync_tag == 2) self.sync_time();
        }, start_time_2);
        if(type != 1){
          this.now_player_com = 1;
        }
        
        this.play_status = 1;
      },
      change_song(com){
        let audio1 = document.getElementById("player1");
        let audio2 = document.getElementById("player2");
        let audio1_targetvol = 0;
        let audio2_targetvol = 0;
        let change_tag = 0;
        
        

        if(com == 1 && com != this.now_player_com && this.now_player_com >= 0){
          audio1_targetvol = 1;
          audio2_targetvol = 0;
          change_tag = 1;

        }else if(com == 2 && com != this.now_player_com && this.now_player_com >= 0){
          audio1_targetvol = 0;
          audio2_targetvol = 1;
          change_tag = 1;

        }else if(com == 3 && com != this.now_player_com && this.now_player_com >= 0){
          audio1_targetvol = 0.75;
          audio2_targetvol = 0.75;
          change_tag = 1;
        }
        console.log('change_tag:'+change_tag+' this.now_player_com '+this.now_player_com);
        if(change_tag == 1){
          this.now_player_com = 0 ;
          this.play_status = 2;
          console.log('this.now_player_com = 0 ;');
          let now_1_vol = audio1.volume;
          let now_2_vol = audio2.volume;
          let now_1_vol_s = (now_1_vol - audio1_targetvol)/this.mix_times;
          let now_2_vol_s = (now_2_vol - audio2_targetvol)/this.mix_times;
          console.log(now_1_vol,now_2_vol,now_1_vol_s,now_2_vol_s);


          let timer1 = setInterval(function () {
            if(audio1.volume - now_1_vol_s < 0){
              audio1.volume = 0;
            }if(audio1.volume - now_1_vol_s > 1){
              audio1.volume = 1;
            }else{
              audio1.volume = audio1.volume - now_1_vol_s;
            }
            //console.log('A1:'+audio1.volume);
          }, this.mix_times_s)

          let timer2 = setInterval(function () {
            if(audio2.volume - now_2_vol_s < 0){
              audio2.volume = 0;
            }if(audio2.volume - now_2_vol_s > 1){
              audio2.volume = 1;
            }else{
              audio2.volume = audio2.volume - now_2_vol_s;
            }
            //console.log('A2:'+audio2.volume);
          }, this.mix_times_s)

          var self = this;

          setTimeout(function (){
            audio1.volume = audio1_targetvol;
            audio2.volume = audio2_targetvol;
            clearInterval(timer1);
            clearInterval(timer2);
            console.log('Fin:' + com);
            change_tag = 0;
            self.now_player_com = com;
            self.play_status = 1;
          }, (this.mix_times_s*this.mix_times)+75)
          


        }
      },
      test_sync_time(){
        let audio1 = document.getElementById("player1");
        let audio2 = document.getElementById("player2");

        if(Math.abs(audio2.currentTime - (audio1.currentTime + (-this.now_music[4]/1000))) > 0.001){
          this.sync_time();
          var self = this;
          setTimeout(function () {
            if(Math.abs(audio2.currentTime - (audio1.currentTime + (-self.now_music[4]/1000))) > 0.0005){
              console.log("Abs > 0.0005,ReChange.")
              self.sync_time();
            }
          }, 300);
        }

      },
      sync_time(){
        let audio1 = document.getElementById("player1");
        let audio2 = document.getElementById("player2");

        audio2.currentTime = audio1.currentTime + (-this.now_music[4]/1000);
      },
      get_time(){
        let audio1 = document.getElementById("player1");
        let audio2 = document.getElementById("player2");

        //audio2.currentTime = audio1.currentTime + (this.now_music[5]/1000);

        console.log(audio2.currentTime - (audio1.currentTime + (-this.now_music[4]/1000)));

        console.log(audio1.currentTime,audio1.currentTime + (-this.now_music[4]/1000));
        console.log(audio2.currentTime);
      },
      play_ended(){
        let audio1 = document.getElementById("player1");
        let audio2 = document.getElementById("player2");
        audio1.pause();
        audio2.pause();
        audio1.currentTime = 0;
        audio2.currentTime = 0;
        this.play_status = 0;
        //this.play_music
        //loop
        if(this.loop_play == true){
          this.play_music(1);
        }
      },
      change_mix_time(){
        if(this.mix_time_sum % 100 != 0){
          this.mix_time_sum = this.mix_time_sum - (this.mix_time_sum % 100)
        }
        this.mix_times = this.mix_time_sum / 100; 
      }

      //http://music.163.com/song/media/outer/url?id=1956543025
    }
}
</script>

<style>
/*
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
*/

 .music-card {
    max-width: 480px; 
    margin: 10px 0px 10px 0px; 
    
  }
  .setting-size{
    font-size: 0.9em;
  }
</style>
