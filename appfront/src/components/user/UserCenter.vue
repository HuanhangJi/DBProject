<template>
    <div>
    <div style="width: 100%;margin: auto;height: 750px">
        <div>
            <Header style="width: 100%"></Header>
        </div>
        <div style="width: 70%;margin: auto">
            <el-menu
                    default-active='1'
                    class="el-menu-vertical-demo"
                    @open="handleOpen"
                    @close="handleClose"
                    background-color="#ffffff"
                    text-color="#000000"
                    active-text-color="#1baeae"
                    >
                <h3>个人中心</h3>
                <el-menu-item index='1' v-on:click="getIndex1">
                    <i class="el-icon-user"></i>
                    <span slot="title">个人信息</span>
                </el-menu-item>
                <el-menu-item index='2' v-on:click="getIndex2">
                    <i class="el-icon-s-goods"></i>
                    <span slot="title">收藏夹</span>
                </el-menu-item>
                <el-menu-item index='3' v-on:click="getIndex3">
                    <i class="el-icon-goods"></i>
                    <span slot="title">浏览记录</span>
                </el-menu-item>
                <el-menu-item index='4' v-on:click="getIndex4">
                    <i class="el-icon-goods"></i>
                    <span slot="title">我的酒店</span>
                </el-menu-item>
                <el-menu-item index='5' v-on:click="logout">
                    <i class="el-icon-switch-button"></i>
                    <span slot="title">登出</span>
                </el-menu-item>
            </el-menu>
        </div>
        <div style="width: 70%;margin: auto">
            <div v-show = "this.card1">
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span class="cardTitle">我的资料</span>
                        <el-button v-on:click="updateUser" class="cardHeadButton" type="text">更改个人信息</el-button>
                    </div>
                    <div>
                        <div class="info_item">
                            <span class="column1">用户名</span>
                            <span class="column2">{{this.user.username}}</span>
                        </div>
                        <div class="info_item">
                            <span class="column1">邮箱</span>
                            <span class="column2">{{this.user.email}}</span>
                        </div>
                        <div class="info_item">
                            <span class="column1">用户类型</span>
                            <span class="column2">{{this.user.type === 1 ? '店家' : '普通用户'}}</span>
                        </div>
                        <div class="info_item">
                            <span class="column1">注册时间</span>
                            <span class="column2">{{this.user.date}}</span>
                        </div>
                        <div>
                            <UserInfoForm @myConfirm="loadUserInfo" ref="UserInfoEditArea"></UserInfoForm>
                        </div>
                    </div>
                </el-card>
            </div>
            <div v-show = "this.card2">
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span class="cardTitle">收藏夹</span>
                        <el-button v-on:click="delAllCollections" class="cardHeadButton" type="text">全部删除</el-button>
                    </div>
                    <div v-for="item in collections.slice((currentPage-1)*pagesize,currentPage*pagesize)" :key="item.id" class="collection">
                        <div style="height: 100px;width: 20%;float: left">
                            <img style="height: 100px;width: 100px"
                            :src="item.piclink"
                            />
                        </div>
                        <div class="collectionInfo">
                            <div class="collectionName">
                                <span>{{item.name}}</span>
                            </div>
                            <div style="width: 100%;">
                                <div style="float: left;width: 40%">
                                    <span class="collectionPriceLabel">最新价格：</span>
                                    <span class="collectionPrice">{{item.newestprice}}</span>
                                </div>
                                <div style="float: right;width: 60%">
                                    <span class="collectionPriceLabel">收藏时间：</span>
                                    <span class="collectionPrice">{{item.time}}</span>
                                </div>
                            </div>
                        </div>
                        <div style="height: 100px;width: 20%;float: right">
                            <div style="height: 50px">
                                <el-button v-on:click="getDetail(item)"  class="myButton"  type = "detail" icon="el-icon-share" >详情</el-button>
                            </div>
                            <div>
                                <el-button v-on:click="delCollection(item)" class="myButton"  type = "detail" icon="el-icon-delete">删除</el-button>
                            </div>
                        </div>
                    </div>
                    <div style="">
                        <el-pagination
                                @current-change="handleCurrentChange"
                                :current-page="currentPage"
                                :page-size="pagesize"
                                :total="collections.length">
                        </el-pagination>
                    </div>
                </el-card>
            </div>
            <div v-show = "this.card3">
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span class="cardTitle">浏览记录</span>
                        <el-button v-on:click="delAllHistories" class="cardHeadButton" type="text">全部删除</el-button>
                    </div>
                    <div v-for="item in this.histories.slice((currentPageHis-1)*pagesizeHis,currentPageHis*pagesizeHis)" :key="item.id" class="history">
                            <div style="height: 100px;width: 20%;float: left;">
                                <img style="height: 100px;width: 100px"
                                     :src="item.piclink"
                                />
                            </div>
                            <div class="collectionInfo">
                                <div class="collectionName">
                                    <span>{{item.name}}</span>
                                </div>
                                <div style="width: 100%">
                                    <div style="float: left;width: 40%">
                                        <span class="collectionPriceLabel">最新价格：</span>
                                        <span class="collectionPrice">{{item.newestprice}}</span>
                                    </div>
                                    <div style="float: right;width: 60%">
                                        <span class="collectionPriceLabel">浏览时间：</span>
                                        <span class="collectionPrice">{{item.time}}</span>
                                    </div>
                                </div>
                            </div>
                            <div style="height: 100px;width: 20%;float: right">
                                <div style="height: 50px">
                                    <el-button v-on:click="getDetail(item)" class="myButton"  type = "detail" icon="el-icon-share" >详情</el-button>
                                </div>
                                <div>
                                    <el-button v-on:click="delHistory(item)" class="myButton"  type = "detail" icon="el-icon-delete">删除</el-button>
                                </div>
                            </div>
                        </div>
                        <div style="">
                            <el-pagination
                                    @current-change="handleCurrentChangeHis"
                                    :current-page="currentPageHis"
                                    :page-size="pagesizeHis"
                                    :total="histories.length">
                            </el-pagination>
                        </div>
                </el-card>
            </div>
        <div v-show = "this.card4">
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span class="cardTitle">我的酒店</span>
                        <el-button v-on:click="updateHotel" class="cardHeadButton" type="text">更改酒店信息</el-button>
                    </div>
                    <div>
                        <div class="info_item">
                            <span class="column1">酒店名</span>
                            <span class="column2">{{this.user.hotel.name}}</span>
                        </div>
                        <div class="info_item">
                            <span class="column1">酒店地址</span>
                            <span class="column2">{{this.user.hotel.address}}</span>
                        </div>
                        <div class="info_item">
                            <span class="column1">酒店经纬度</span>
                            <span class="column2">{{this.user.hotel.loc}}</span>
                        </div>
                        <div class="info_item">
                            <span class="column1">酒店注册时间</span>
                            <span class="column2">{{this.user.hotel.regTime}}</span>
                        </div>
                        <div v-for="item in this.user.hotel.room" :key="item.id">
                          <div class="collectionInfo">
                            <div class="collectionName">
                                <span>{{item.type}}占用比</span>
                                <el-progress width="60" type="circle" :percentage="(item.已预约/item.总量).toFixed(3)*100" :color="colors"></el-progress>
                            </div>
                        </div>
                            
                        </div>
                        <div>
                            <HotelInfoForm @myConfirm="loadUserInfo" ref="HotelInfoEditArea"></HotelInfoForm>
                        </div>
                    </div>
                </el-card>
            </div>
          </div>
        </div>
    <div>
        <Footer></Footer>
    </div>
    </div>
</template>

<script>
    import Header from "@/components/home/Header";
    import Footer from "@/components/home/Footer";
    import UserInfoForm from "@/components/user/UserInfoForm";
    import HotelInfoForm from "@/components/user/HotelInfoForm"
    export default {
        name: "UserCenter",
        components: { UserInfoForm, Header, Footer, HotelInfoForm},
        data() {
          console.log(this.$store.state.user.hotel);
            return {
                card1:true,
                card2:false,
                card3:false,
                card4:false,
                user: {
                    id:this.$store.state.user.id,
                    username: this.$store.state.user.username,
                    email: this.$store.state.user.email,
                    date: this.$store.state.user.date,
                    type: this.$store.state.user.type,
                    hotel: this.$store.state.user.loc_hotel,
                },
                collections:[],
                histories:[],
                ColAndHis:{
                    userid:this.$store.state.user.id,
                    comid:this.$store.state.hotel.cid,
                    time:this.getMyTime()
                },
                currentPage:1,
                pagesize:4,
                currentPageHis:1,
                pagesizeHis:4,
                colors: [
              {color: '#ff0000', percentage: 100},
              {color: '#ff8800', percentage: 80},
              {color: '#ffff00', percentage: 60},
              {color: '#88ff00', percentage: 40},
              {color: '#00ff00', percentage: 20}
        ]
            }
        },
        created() {
            this.card1 = true
            this.card2 = false
            this.card3 = false
            this.card4 = false
            this.getAllCollections()
            this.getAllHistories()
        },
        methods: {
            updateUser() {
                this.$refs.UserInfoEditArea.dialogFormVisible = true;
            },
            updateHotel() {
                console.log("click on updateHotel")
                this.$refs.HotelInfoEditArea.dialogFormVisible = true;
            },
            delAllHistories() {
                this.$axios.post('/delAllHistories/', this.ColAndHis
                ).then(
                    resp=>{
                        if (resp && resp.data.code === 200) {
                            this.getAllHistories()
                            this.$alert('全部删除成功', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                        else {
                            this.$alert('网络问题，删除失败', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                    })
            },
            delHistory(item) {
                this.$axios.post('/delHistory/', {
                    userid:this.$store.state.user.id,
                    hotel_id:item.id,
                    time:this.getTime
                }).then(
                    resp=>{
                        if (resp && resp.data.code === 200) {
                            this.getAllHistories()
                            this.$alert('删除成功', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                        else {
                            this.$alert('网络问题，删除失败', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                    })
            },
            delCollection(item) {
                this.$axios.post('/delCollection/', {
                        userid:this.$store.state.user.id,
                        hotel_id:item.id,
                        time:this.getTime
                }).then(
                    resp=>{
                        if (resp && resp.data.code === 200) {
                            this.getAllCollections()
                            this.$alert('删除成功', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                        else {
                            this.$alert('网络问题，删除失败', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                })
            },
            delAllCollections() {
                this.$axios.post('/delAllCollections/', this.ColAndHis
                ).then(
                    resp=>{
                        if (resp && resp.data.code === 200) {
                            this.getAllCollections()
                            this.$alert('全部删除成功', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                        else {
                            this.$alert('网络问题，删除失败', '提示', {
                            confirmButtonText: '确定'
                        })
                        }
                    })
            },
            getAllCollections() {
                this.$axios.post('/getAllCollections/', this.ColAndHis)
                .then(resp=>{
                    if (resp && resp.data.code === 200) {
                        this.collections = resp.data.data
                    }
                    else {
                        console.log(resp.data.code);
                        this.$router.replace('/mainpage')
                    }
                })
            },
            getAllHistories() {
                this.$axios.post('/getAllHistories/', this.ColAndHis)
                    .then(resp=>{
                        if (resp && resp.data.code === 200) {
                            this.histories = resp.data.data
                        }
                        else {
                            console.log(resp.data.code);
                            this.$router.replace('/mainpage')
                        }
                    })
            },
            getDetail(item) {
                this.$store.commit('setInfo',item)
                this.$router.replace('/HotelInfo')
            },
            handleOpen(key, keyPath) {
                console.log(key, keyPath);
            },
            handleClose(key, keyPath) {
                console.log(key, keyPath);
            },
            getIndex1() {
                this.card1 = true
                this.card2 = false
                this.card3 = false
                this.card4 = false
            },
            getIndex2() {
                this.card1 = false
                this.card2 = true
                this.card3 = false
                this.card4 = false
            },
            getIndex3() {
                this.card1 = false
                this.card2 = false
                this.card3 = true
                this.card4 = false
            },
            getIndex4() {
              this.card1 = false
              this.card2 = false
              this.card3 = false
              this.card4 = true
            },
            logout() {
                this.$store.commit('logout')
                this.$router.replace('/mainpage')
            },
            handleCurrentChange: function (currentPage) {
                this.currentPage = currentPage
            },
            handleCurrentChangeHis: function (currentPageHis) {
                this.currentPageHis = currentPageHis
            },
            loadUserInfo() {
                console.log(this.$store.state.user.username)
                this.user.id = this.$store.state.user.id
                this.user.username = this.$store.state.user.username
                this.user.email = this.$store.state.user.email
                this.user.date = this.$store.state.user.date
                this.user.type = this.$store.state.user.type
                this.user.hotel = this.$store.state.user.hotel
            }
        }
    }
</script>

<style scoped>
    .collectionPrice {
        float: left;
        color: #1baeae;
    }
    .collectionPriceLabel {
        float: left;
        color: #cac6c6;
    }
    .collectionInfo {
        height: 100px;
        width: 60%;
        float: left;
        font-size: 18px;
    }
    .collectionName {
        width: 100%;
        font-weight: bold;
        height: 70%;
    }
    .el-button--detail:focus,
    .el-button--detail:hover {
        background: #48D1CC;
        border-color: #48D1CC;
        color: #fff;
    }

    .el-button--detail {
        color: #FFF;
        background-color: #20B2AA;
        border-color: #20B2AA;
    }
    .myButton {
        background-color: #1baeae;
        color: white;
    }
    .info_item {
        height: 45px;
        line-height: 60px;
        width: 90%;
        background: #fdfdfd;
        border: 1px solid #aaa;
        margin-top: 10px;
        margin-bottom: 10px;
        border-radius: 3px;
        font-size: 20px;
        color: #cac6c6;
        float: left;
    }
    .cardHeadButton{
        float: right;
        padding: 3px 0;
        color: #1baeae;
        font-size: 20px;
    }
    .clearfix{
        height: 10px;
    }
    .history {
        height: 120px;
        width: 100%;
    }
    .collection {
        height: 120px;
        width: 100%;
    }
    .column1{
        width: 30%;
        text-align: left;
    }
    .column2{
        width: 60%;
        text-align: left
    }
    .cardTitle{
        font-size: 20px;
        float: left;
    }
    .el-menu-vertical-demo {
        font-size: 20px;
        width: 20%;
        margin-top: 20px;
        float: left;
        height: 600px;
    }
    .box-card {
        width: 78%;
        margin-top: 20px;
        height: 598px;
        float: right;
    }
    span {
        display: inline-block;
        line-height: 20px;
        padding: 5px 0;
        margin-right: 10px;
        word-wrap: break-word;
        word-break: break-all;
        overflow: hidden;
        text-align: left;
    }

</style>