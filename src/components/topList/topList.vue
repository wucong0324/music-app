<template>
    <transition name="slide">
        <musicList
                :rank="rank"
                :songs="songs"
                :title="title"
                :bgImage="bgImage"></musicList>
    </transition>
</template>
<script>
    import musicList from '@/components/musicList/musicList'
    import {mapGetters} from 'vuex'
    import {getMusicList} from '@/api/rank'
    import {ERR_OK} from '@/api/config'
    import {createSong} from '@/common/js/song'
    export default{
        data(){
            return {
                songs: [],
                rank: true
            }
        },
        created(){
            this._getMusicList();
        },
        methods: {
            _getMusicList(){
                if(!this.topList.id){
                    this.$router.push('/rank');
                    return
                }
                getMusicList(this.topList.id).then((res) => {
//                    console.log(res);
                    this.songs = this._normalizeSongs(res.songlist);
                })
            },
            _normalizeSongs(list){
                let ret = [];
                list.forEach((item) => {
                    const musicData = item.data;
                    if(musicData.songid && musicData.albumid){
                        ret.push(createSong(musicData));
                    }
                });
                return ret
            }
        },
        computed: {
            title(){
                return this.topList.topTitle
            },
            bgImage(){
                if(this.songs.length){
                    return this.songs[0].image
                }
                return ''
            },
            ...mapGetters([
                'topList'
            ])
        },
        components: {
            musicList
        }
    }
</script>
<style scoped lang="scss">
    .slide-enter-active, .slide-leave-active {
        transition: all .3s;
    }

    .slide-enter, .slide-leave-to {
        transform: translate3d(100%, 0, 0);
    }
</style>