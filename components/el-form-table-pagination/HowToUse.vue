<template>
    <el-form-table-pagination
        ref="elFTP"
        :formData="formData"
        :ruleForm="ruleForm"
        :columnData="columnData"
        :tableData="tableData"
        :total="total"
        :rowClick= "true"
        @handleChange="handleChangeData"
        @query="query">
        <template slot="formButtons">
            <el-button type="primary" @click="query()">快点我</el-button>
        </template>
        <template slot="tableButtons">
            <el-button type="primary" @click="saoOperation()" style="float:right">操作</el-button>
        </template>
    </el-form-table-pagination>
</template>

<script>
import elForm from '@/components/el-form-table-pagination';
export default {
    components: {
        'el-form-table-pagination':elForm
    },
    data(){
        return{
            rules:{},
            ruleForm:{},
            formData:[{
                prop:'loanId',
                label:'借据号',
                type:'input',
                placeholder:'请输入借据号'
            },{
                prop:'prod',
                label:'产品',
                type:'select',
                options:[{
                    key:'001',
                    label:'车主贷'
                }],
                placeholder:'请选择产品'
            },{
                prop:'startDate',
                label:'交易开始日期',
                type:'datepicker',
                dateOptions:{
                    disabledDate: (data) => {
                        return (data.getTime()>Date.now());
                    }
                },
                placeholder:'请选择日期'
            }],
            tableData:[{
                loanId:'0001',
                name:'尼古拉斯返',
                certNo:'111111111111111',
                repayTime:'2020-09-01'
            },{
                loanId:'0002',
                name:'克里斯蒂斌',
                certNo:'2222222222222222',
                repayTime:'2020-10-01'
            },{
                loanId:'0003',
                name:'亚历山大辉',
                certNo:'33333333333333333',
                repayTime:'2020-11-01'
            }], // 表格全部数据
            columnData:[{
                prop:'loanId',
                label:'借据号'
            },{
                prop:'name',
                label:'客户姓名'
            },{
                prop:'certNo',
                label:'客户身份证',
                filters:'certNo',
            },{
                prop:'repayTime',
                label:'交易时间'
            }], // 表头数据
            total: 60, // 数据总数
            dateOptions: {
                disabledDate: (data) => {
                    return new Date(data.getFullYear(),(data.getMonth()),data.getDate()).getTime()!=new Date(data.getFullYear(),(data.getMonth()+1),0).getTime()
                    //return (data.getTime()>Date.now())||((data.getDate()!='1')&&(data.getDate()!='14')&&(data.getDate()!='16')&&(data.getDate()!='19')&&(data.getDate()!='22'));
                }
            },
        }
    },
    methods: {
        query(params){
            this.getNewData(params)
        },
        handleChangeData (params) {
            this.getNewData(params)
        },
        getNewData(params){
            console.log(params)
            // this.$axios.get("/AC/standingBook/ACM0108", {params:params}).then(data => {
            //     this.tableData = data.responseData.data;
            //     this.total=data.responseData.totalNum;
            // });
        },
        saoOperation(){
            this.$refs.elFTP.rowClickedCheck('console.log(this.currentRow)')
        }
    },
    mounted:function(){
        //如果异步导入组件懒加载的话此处不可使用this.$refs.XXXX
        var params={
            pageSize:10,
            pageNum:0
        }
        this.getNewData(params)
    },
}
</script>