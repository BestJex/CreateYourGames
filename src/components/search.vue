<template>
    <div class="search">
        <input type="text" 
        @click.stop="searchDisplay" placeholder="输入需要搜索的游戏" 
        v-model="search" 
        @input='inputSearch(search)'>
        <el-button type="info" size="small" @click='searchFather'>搜索</el-button>
        <div class="searchContent" v-if="$store.state.searchDisplay">
            <ul>
                <li 
                v-for="(item,index) in searchList(search)" 
                :key="index"
                @click="updateSearch(index,search)">
                    {{item}}
                </li>
            </ul>
        </div>
    </div>
</template>
<script>
export default {
    data(){
        return{
            search:'',
            //搜索快那个的显式隐藏
        }
    },
    props:['gameList'],
    methods: {
        searchDisplay(){
            this.$store.commit('changeSearch',true)
        },
        updateSearch(index,value){
            //先匹配到输入内容中的所有元素，然后从现有的元素中选取第几个作为内容放到输入框
            this.search=this.searchList(value)[index]
            this.$store.state.searchDisplay=false
        },
        //输入内容时的变化
        inputSearch(value){
            if(this.search==''){
                this.$store.state.searchDisplay=true
            }
        },
        //输入内容时下拉框的内容
        searchList(value){
            //定义一个可以暂时存放数据的容器
            var a=[]
            this.gameList.filter(item=>{
                if(item.gameName.includes(value)){
                    a.push(item.gameName)
                }
            })
            return a
        },
        searchFather(){
            this.$emit('search',this.search)
        }
    },
}
</script>
<style lang="scss" scoped>
    .search{
        width: 30%;height: 50px;
        position: relative;
        margin-top: 15px;
        display: flex;
        align-items: center;
        input{
            width: 250px;height: 32px;
            border: 1px solid #d8dce0;
            outline: none;
            color: #1e262c;
            font-size: 14px;
            padding-left: 10px;
            border-radius: 3px;
            background: #d8dce0;
        }
        .el-button{
            border: none;
            background-color: #d8dce0;
            margin-left: 10px;
            color: black;
        }
        .searchContent{
            width: 250px;max-height: 175px;
            overflow: auto;
            background-color: white;
            position: absolute;
            top: 50px;
            background-color: rgba(255,255,255,0.2);
            ul{
                li{
                    display: flex;
                    align-items: center;
                    padding-left: 15px;
                    background-color: rgba(0,0,0,.6);
                    color: white;
                    height: 35px;
                    border-bottom: 1px solid #ccc;
                    cursor: pointer;
                    &:hover{
                        background-color: yellowgreen;
                    }
                }
            }
        }
    }
</style>