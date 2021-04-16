<template>
    <div>
        <el-table
            :data="tableData" 
            :height="$attrs['height']" 
            :highlight-current-row="$attrs['highlight-current-row']"
            v-bind="$attrs"
            v-on="$listeners"
            @row-click="rowClick"
            :header-cell-style="{background:'#F3F4F7',height:'40px'}">
            <template v-for="item in columnData">
                <slot v-if="item.filters">
                    <el-table-column
                        :type="item.type"
                        :prop="item.prop"
                        :label="item.label">
                        <template slot-scope="scope">
                            <span>{{ filter(scope.row[scope.column.property],item.filters) }}</span>
                        </template>
                    </el-table-column>
                </slot>
                <slot v-else>
                    <el-table-column
                        :type="item.type"
                        :prop="item.prop"
                        :label="item.label">
                    </el-table-column>
                </slot>
            </template>
        </el-table>
        <div class="tableButtons">
            <slot name="tableButtons" style="float:right"></slot>
        </div>
        <el-pagination
            class="pagination"
            layout="total, sizes, prev, pager, next, jumper"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="currentPage"
            :page-sizes="[10, 15, 20]"
            :page-size="pagesize"
            :total="total"
        ></el-pagination>
    </div>
</template>

<script>
    export default {
        props: {
            // 分页数据总数
            total: {
                type: Number,
                default: 1000,
                required: false
            },
            // 单页数据量
            pagesize: {
                type: Number,
                default: 10,
                required: false
            },
            // 当前页码
            currentPage: {
                type: Number,
                default: 1,
                required: false
            },
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
            },

        },
        data() {
            return {
                currentRow:{}
            }
        },
        methods: {
            rowClick(row){
                this.currentRow=row;
            },
            rowClickedCheck(str){
                if(JSON.stringify(this.currentRow)!='{}'){
                    eval(str);
                    return true;
                }else{
                    this.$message.error('请选择某一行数据')
                    return false;
                }
            },
            handleCurrentChange: function (currentPage) {
                this.$emit('handleChange', this.pagesize, currentPage)
            },
            handleSizeChange: function (pageSize) {
                this.$emit('handleChange', pageSize, this.currentPage)
            },
            filter(val,filterName) {
                let filterObj = this.$options.filters[filterName]
                return filterObj(val)
            }
        }
    }
</script>

<style scoped>
.tableButtons{
    float: right;
    margin-top:20px;
}
.pagination{
    text-align: center;
    margin-top:20px;
    margin-bottom: 30px;
}
</style>