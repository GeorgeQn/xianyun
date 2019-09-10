<template>
  <section class="contianer">
        <el-row  type="flex" justify="space-between">

            <!-- 顶部过滤列表 -->
            <div class="flights-content">
                <!-- 过滤条件 -->
                <FlightsFilters
                :data="cacheFlightsData" @setDataList="setDataList"
                />
                
                <!-- 航班头部布局 -->
                <FlightsListHead/>
                
                <!-- 航班信息 -->
                <div>
                    <FlightsItem
                    v-for="(item,index) in dataList"
                    :key="index"
                    :data="item"
                    />
                

                    <!-- 分页 -->
                    <!-- size-change：每页条数切换时候触发 -->
                    <!-- current-change：页码切换时候触发 -->
                    <!-- current-page: 当前的页码 -->
                    <!-- page-size: 当前显示的条数 -->
                    <!-- total: 总条数 -->
                    <el-pagination
                        @size-change="handleSizeChange"
                        @current-change="handleCurrentChange"
                        :current-page="pageIndex"
                        :page-sizes="[5, 10, 15, 20]"
                        :page-size="pageSize"
                        layout="total, sizes, prev, pager, next, jumper"
                        :total="total">
                    </el-pagination>
                </div>
            </div>

            <!-- 侧边栏 -->
            <div class="aside">
                <!-- 侧边栏组件 -->
                <FlightsAside/>
            </div>
        </el-row>
    </section>
</template>

<script>
import FlightsListHead from '@/components/air/flightsListHead'
import FlightsItem from '@/components/air/flightsItem'
import FlightsFilters from '@/components/air/flightsFilters'
import FlightsAside from '@/components/air/flightsAside'


export default {
    data(){
        return{
            //机票列表总数据
            flightsData:{
                info:{},
                options:{}
            },
            cacheFlightsData:{
                info:{},
                options:{}
            },
            pageIndex:1,
            pageSize:5,
            total:0,
            
            dataList:[],
        }
    },
    components:{
        FlightsListHead,
        FlightsItem,
        FlightsFilters,
        FlightsAside
    },
    watch: {
        // watch可以监听this下的所有属性
        $route(){
            // 请求航班列表数据
            this.getData();
        }
    },
    methods:{

        handleSizeChange(val){
            this.pageSize = val
            this.dataList = this.flightsData.flights.slice(0,val)
        },
        handleCurrentChange(val){
            this.pageIndex = val
            this.dataList = this.flightsData.flights.slice(this.pageSize*(this.pageIndex-1),this.pageIndex * this.pageSize)
        },
        setDataList(arr){
            this.flightsData.flights = arr

            this.dataList = this.flightsData.flights.slice( 
                (this.pageIndex - 1) * this.pageSize, 
                this.pageIndex * this.pageSize 
            );
        },
        getData(){
            this.$axios({
            url:'/airs',
            params:this.$route.query
        }).then(res => {
            console.log(res)
            this.flightsData = res.data
            this.cacheFlightsData = {...res.data}
            this.total = this.flightsData.flights.length
            this.dataList = this.flightsData.flights.slice(0,this.pageSize)
        })
        }
    },
    mounted(){
        this.getData()    
    }
}
</script>

<style scoped lang="less">
    .contianer{
        width:1000px;
        margin:20px auto;
    }
    .flights-content{
        width:745px;
        font-size:14px;
    }
    .aside{
        width:240px;
    } 
</style>