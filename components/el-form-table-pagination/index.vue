<template>
    <div>
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm">
            <el-row :gutter="20">
                <template v-for="item in formData">
                    <slot v-if="item.type=='input'">
                        <el-col :span="6">
                            <el-form-item
                                :prop="item.prop"
                                :label="item.label">
                                <el-input v-model="ruleForm[item.prop]" :placeholder="'请输入'+item.label" clearable></el-input>
                            </el-form-item>
                        </el-col>
                    </slot>
                    <slot v-else-if="item.type=='select'">
                        <el-col :span="6">
                            <el-form-item
                                :prop="item.prop"
                                :label="item.label">
                                <el-select v-model="ruleForm[item.prop]" :placeholder="'请选择'+item.label" style="width:100%" clearable>
                                    <el-option
                                        v-for="subItem in item.options"
                                        :key="subItem.key"
                                        :label="subItem.label"
                                        :value="subItem.key">
                                    </el-option>
                                </el-select>
                            </el-form-item>
                        </el-col>
                    </slot>
                    <slot v-else-if="item.type=='datepicker'">
                        <el-col :span="6">
                            <el-form-item
                                :prop="item.prop"
                                :label="item.label">
                                <el-date-picker type="date" :placeholder="'请选择'+item.label" v-model="ruleForm[item.prop]"  style="width:100%" value-format="yyyy-MM-dd" :picker-options="item.dateOptions"></el-date-picker>
                            </el-form-item>
                        </el-col>
                    </slot>
                </template>
            </el-row>
            <el-form-item>
                <div style="text-align:center">
                    <el-button type="primary" @click="query()">查询</el-button>
                    <el-button @click="resetForm('ruleForm')">重置</el-button>
                    <slot name="formButtons"></slot>
                </div>
            </el-form-item>
        </el-form>
        <el-table
            :data="tableData"
            :height="$attrs['height']" 
            v-bind="$attrs"
            v-on="$listeners"
            :highlight-current-row="$attrs['highlight-current-row']"
            @row-click="handleRowClick"
            :header-cell-style="{background:'#F3F4F7',height:'40px'}">
            <template v-for="(item, index) in columnData">
                <slot v-if="item.filters">
                    <el-table-column
                        :prop="item.prop"
                        :key="index"
                        :label="item.label">
                        <template slot-scope="scope">
                            <span>{{ filter(scope.row[scope.column.property],item.filters) }}</span>
                        </template>
                    </el-table-column>
                </slot>
                <slot v-else>
                    <el-table-column
                        :prop="item.prop"
                        :key="index"
                        :label="item.label">
                    </el-table-column>
                </slot>
            </template>
        </el-table>
        <div class="tableButtons">
            <slot name="tableButtons"></slot>
        </div>
        <el-pagination
            class="pagination"
            style="margin-top:20px"
            layout="total, sizes, prev, pager, next, jumper"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="currentPage"
            :page-sizes="[10, 15, 20]"
            :page-size="pageSize"
            :total="total"
        ></el-pagination>
    </div>
</template>

<script>
    export default {
        props: {
            // 表单规则
            rules:{
                type: Object,
                default:()=>{},
                required: false
            },
            // 表单输入选项
            formData: {
                type: Array,
                default:()=>[],
                required: false
            },
            // 分页数据总数
            total: {
                type: Number,
                default: 1000,
                required: false
            },
            rowClick:{
                type: Boolean,
                default: false,
                required: false
            },
            // // 单页数据量
            // pagesize: {
            //     type: Number,
            //     default: 10,
            //     required: false
            // },
            // // 当前页码
            // currentPage: {
            //     type: Number,
            //     default: 1,
            //     required: false
            // },
            // 表格数据
            tableData: {
                type: Array,
                default:()=>[],
                required: false
            },
            // 表头数据
            columnData: {
                type: Array,
                default:()=>[],
                required: false
            }
        },
        data() {
            return {
                ruleForm:{},
                ruleFormBackups:{},
                pageSize:10,
                currentPage:1,
                currentRow:{}
            }
        },
        methods: {
            handleCurrentChange: function (currentPage) {
                this.currentPage=currentPage;
                var params={
                    ...this.ruleFormBackups,
                    pageSize:this.pageSize,
                    pageNum:(currentPage-1)*this.pageSize
                };
                this.$emit('handleChange',params)
            },
            handleSizeChange: function (pageSize) {
                this.pageSize=pageSize;
                var params={
                    ...this.ruleFormBackups,
                    pageSize:pageSize,
                    pageNum:(this.currentPage-1)*pageSize
                };
                this.$emit('handleChange', params)
            },
            filter(val,filterName) {
                let filterObj = this.$options.filters[filterName];
                return filterObj(val)
            },
            resetForm(formName) {
                Object.keys(this.ruleForm).forEach(key=>{this.ruleForm[key]=''});
                this.$refs[formName].resetFields();
            },
            handleRowClick(row){
                this.currentRow=row;
            },
            rowClickedCheck(str){
                if(JSON.stringify(this.currentRow)!='{}'){
                    eval(str)
                    return true;
                }else{
                    this.$message.error('请选择某一行数据')
                    return false;
                }
            },
            query(){
                this.ruleFormBackups=JSON.parse(JSON.stringify(this.ruleForm));
                this.currentPage=1;
                var params={
                    ...this.ruleFormBackups,
                    pageSize:this.pageSize,
                    pageNum:(this.currentPage-1)*this.pageSize
                };
                this.$emit('query', params)
            }
        },
    }
</script>

<style scoped>
.tableButtons{
    float: right;
    margin-top:20px;
}
.pagination{
    text-align: center;
    margin-bottom: 30px;
}
</style>