<template>


    <el-container style="height: 500px; border: 1px solid #eee">
        <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
            <el-menu>
                <el-submenu index="1">
                    <template slot="title"><i class="el-icon-message"></i>未处理商品</template>

                </el-submenu>
            </el-menu>
        </el-aside>

        <el-container>
            <el-header style="text-align: right; font-size: 12px">
                <el-dropdown>
                   
            
                </el-dropdown>
                <span>{{msg.username}}</span>
            </el-header>
            <!-- 商品浏览主页 -->
            <el-main>
                <el-table :data="commodities">
                    <!-- <el-table-column prop="id" label="日期" width="140">
                    </el-table-column> -->
                    <el-table-column prop="comName" label="商品名称" width="120">
                    </el-table-column>
                    <el-table-column prop="comDescribe" label="商品描述" width="120">
                    </el-table-column>
                    <el-table-column prop="comPrice" label="商品价格">
                    </el-table-column>
                    <el-table-column prop="comReleaseTime" label="商品发布时间">
                    </el-table-column>
                    <el-table-column prop="comStatus" label="商品状态">
                    </el-table-column>
                    <el-table-column label="进行操作" :prop="comID">
                        <template slot-scope="scope">
                            <el-button type="success" @click="changestatus(scope.row)">通过</el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </el-main>
        </el-container>
    </el-container>
</template>
<style>
    .el-header {
        background-color: #B3C0D1;
        color: #333;
        line-height: 60px;
    }

    .el-aside {
        color: #333;
    }
</style>

<script>
    export default {

        //保存用户信息
        state: {
            user: {},
            token: false
        },

        data() {
            const item = {
                date: '2016-05-02',
                name: '王小虎',
                address: '上海市普陀区金沙江路 1518 弄'
            };

            return {

                tableData: Array(20).fill(item),
                msg: this.$route.query.data,
                
                //用户渲染商品列表的数据
                commodities: [{
                    id: "",
                    name: "",
                    describe: "",
                    price: "",
                    releasetime: "",
                    status: ""

                }],


            }
        },
        created() {
            
            this.msg = JSON.parse(window.sessionStorage.getItem('adminobj'))
            //alert("欢迎您:" + this.msg.username)
            console.log(this.msg)
            var _this = this
            axios.get('http://localhost:9090/unhandled/findall').then(function (response) {
                console.log(response)
                _this.commodities = response.data
                // console.log(response)
            })
        },
        methods: {
            changestatus(commodity) {
                console.log(commodity)
                var _data = {};
                _data.comID = commodity.comID
                var sendjson = JSON.stringify(_data)
                axios({
                    //发送http请求
                    method: 'post',
                    url: 'http://localhost:9090/unhandled/changestatus',
                    crossDomain: true,
                    //以map形式发送
                    data: sendjson,


                    headers: {
                        'Content-Type': 'application/json;charset=UTF-8'
                    }
                }).then(response => {


                })
                alert("修改成功")
                location.reload();
            }
        }
    };
</script>