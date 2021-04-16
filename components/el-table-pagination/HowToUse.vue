<template>
    <div>
        <h2>Demo</h2>
        <el-table-pagination
            ref="elTP"
            height="600"
            :columnData="columnData"
            :tableData="tableData"
            :total="total"
            :highlight-current-row= "true"
            :pagesize="pagesize"
            :currentPage="currentPage"
            @handleChange="handleChangeData">
            <template slot="tableButtons">
                <el-button type="primary"  @click="operation()">操作</el-button>
            </template>
        </el-table-pagination>
    </div>
</template>

<script>
export default {
    components: {
        'el-table-pagination': () => import('@/components/el-table-pagination'),
    },
    data(){
        return{
            tableData:[],
            columnData:[{
                prop:'Id',
                label:'序号'
            },{
                prop:'Name',
                label:'名称'
            }],
            total: 0,
            pagesize: 10,
            currentPage: 1 ,
        }
    },
    methods: {
        handleChangeData (pagesize, currentPage) {
            this.pagesize = pagesize;
            this.currentPage = currentPage;
            this.getNewData();
        },
        getNewData(){
            var params={
                pageSize:this.pagesize,
                pageNum:(this.currentPage-1)*this.pagesize
            };
            this.$axios.get("/XXXX/xxxx", {params:params}).then(data => {
                this.tableData = data.responseData.data ; 
                this.total= data.responseData.totalNum ;
            });
        },
        operation(){
            if(this.$refs.elTP.rowClickedCheck()==true){
                console.log("目前选中行的数据为："+this.$refs.elFTP.currentRow);
            }
        }
    },
    mounted:function(){
        this.getNewData();
    },
}
</script>