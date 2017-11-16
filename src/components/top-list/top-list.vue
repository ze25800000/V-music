<template>
  <transition name="slide">
    <music-list :rank="rank" :title="title" :bg-image="bgImage" :songs="songs"></music-list>
  </transition>
</template>

<script type="text/ecmascript-6">
  import MusicList from 'components/music-list/music-list'
  import {mapGetters} from 'vuex'
  import {getMusicListList} from 'api/rank'
  import {ERR_OK} from 'api/config'
  import {createSong} from 'common/js/song'

  export default {
    data() {
      return {
        songs: [],
        rank: true
      }
    },
    created() {
      this._getMusicListList()
    },
    methods: {
      _getMusicListList() {
        if (!this.topList.id) {
          this.$router.push('/rank')
          return
        }
        getMusicListList(this.topList.id)
          .then(res => {
            if (res.code === ERR_OK) {
              this.songs = this._normalizeSongs(res.songlist)
            }
          })
          .catch(err => {
            console.log(err)
          })
      },
      _normalizeSongs(list) {
        let ret = []
        list.forEach(item => {
          const musicData = item.data
          if (musicData.songid && musicData.albumid) {
            ret.push(createSong(musicData))
          }
        })
        return ret
      }
    },
    computed: {
      ...mapGetters([
        'topList'
      ]),
      rank() {
        return this.topList.rank
      },
      title() {
        return this.topList.topTitle
      },
      bgImage() {
        if (this.songs.length) {
          return this.songs[0].image
        }
        return ''
      }
    },
    components: {
      MusicList
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .slide-enter-active, .slide-leave-active
    transition: all 0.3s ease

  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)
</style>
