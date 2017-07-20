<template>
  <div class="singer" ref="singer">
    <list-view :data="singers"></list-view>
  </div>
</template>

<script>
  import {getSingerList} from 'api/singer'
  import { ERR_OK } from 'api/config'
  import Singer from 'common/js/singer'
  import ListView from 'base/listview/listview'

  export default{
  	components:{
  		ListView
    },
    data(){
      return {
        singers: []
      }
    },
    created(){
      this._getSingerList()
    },
    methods: {
      _getSingerList(){
        getSingerList().then(res =>{
          if(res.code == ERR_OK){
            this.singers = this._normalizeSinger(res.data.list)
          }
        })
      },
      _normalizeSinger(list){
        let map = {
          hot : {
            title : "热门",
            data : []
          }
        }
        list.forEach((item, index) => {
          if(index < 10) {
            map.hot.data.push(new Singer({
              id: item.Fsinger_mid,
              name: item.Fsinger_name,
            }))
          }

          let key = item.Findex

          if(!map[key]){
            map[key] = {
              title: key,
              data: []
            }
          }

          map[key].data.push(new Singer({
            id: item.Fsinger_mid,
            name: item.Fsinger_name,
          }))
        })
        let hot = []
        let ret = []

        for(let key in map){
          let val = map[key]
          if(val.title.match(/[a-zA-Z]/)){
          	ret.push(val)
          }else if(val.title == '热门'){
            hot.push(val)
          }
        }

        ret.sort((a, b) => {
        	return a.title.charCodeAt(0) - b.title.charCodeAt(0)
        })

        return hot.concat(ret)
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .singer
    position: fixed
    top: 88px
    bottom: 0
    width: 100%
</style>
