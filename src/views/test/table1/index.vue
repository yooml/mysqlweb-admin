<template>
    <div class="app-container">
        <div class="filter-container">
            <!--el-input :placeholder="$t('table.title')" v-model="listQuery.title" style="width: 200px;" class="filter-item" @keyup.enter.native="handleFilter"/-->
            <!--el-select v-model="list.status" placeholder="status" clearable style="width: 90px" class="filter-item">
                <el-option v-for="item in statusOptions" :key="item" :label="item" :value="item"/>
            </el-select-->
            <!--el-select v-model="listQuery.type" :placeholder="$t('table.type')" clearable class="filter-item" style="width: 130px">
                <el-option v-for="item in calendarTypeOptions" :key="item.key" :label="item.display_name+'('+item.key+')'" :value="item.key"/>
            </el-select>
            <el-select v-model="listQuery.sort" style="width: 140px" class="filter-item" @change="handleFilter">
                <el-option v-for="item in sortOptions" :key="item.key" :label="item.label" :value="item.key"/>
            </el-select-->
            <el-button v-waves class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">search</el-button>
            <!--el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleCreate">{{ $t('table.add') }}</el-button>
            <el-button v-waves :loading="downloadLoading" class="filter-item" type="primary" icon="el-icon-download" @click="handleDownload">{{ $t('table.export') }}</el-button>
            <el-checkbox v-model="showReviewer" class="filter-item" style="margin-left:15px;" @change="tableKey=tableKey+1">{{ $t('table.reviewer') }}</el-checkbox-->
        </div>
        <el-table
                v-loading="listLoading"
                :data="list"
                element-loading-text="Loading"
                border
                fit
                highlight-current-row
                @sort-change="sortChange">
            <!--el-table-column align="center" label="ID" width="95">
                <template slot-scope="scope">
                    {{ scope.row.dbname }}
                </template>
            </el-table-column-->
            <el-table-column label="数据库名">
                <template slot-scope="scope">
                    {{ scope.row.dbname }}
                </template>
            </el-table-column>
            <el-table-column label="Host" width="360" align="center">
                <template slot-scope="scope">
                    <span>{{ scope.row.dbhost }}</span>
                </template>
            </el-table-column>
            <el-table-column label="操作" align="center" width="160" class-name="small-padding fixed-width">
                <template slot-scope="scope">
                    <el-button type="primary" size="mini" @click="handleUpdate(scope.row)">编辑</el-button>
                    <el-button v-if="scope.row.status=='已拥有'" size="mini" type="success" @click="handleModifyStatus(scope.row,'未拥有')">删除
                    </el-button>
                    <el-button size="mini" @click="handleModifyStatus(scope.row,'未拥有')" v-else>申请
                    </el-button>


                    <!--el-button v-if="scope.row.status!='deleted'" size="mini" type="danger" @click="handleModifyStatus(scope.row,'deleted')">{{ $t('table.delete') }}
                    </el-button-->
                </template>
            </el-table-column>
            <!--el-table-column label="数据库账号" width="110" align="center">
                <template-- slot-scope="scope">
                    {{ scope.row.dbuser }}
                </template>
            </el-table-column-->
            <el-table-column class-name="status-col" label="Status" width="110" align="center">
                <template slot-scope="scope">
                    <el-tag :type="scope.row.status | statusFilter">{{ scope.row.status }}</el-tag>
                </template>
            </el-table-column>
            <el-table-column label="数据库类型" width="110" align="center">
                <template slot-scope="scope">
                    {{ scope.row.dbtype }}
                </template>
            </el-table-column>
            <el-table-column align="center" prop="created_at" label="到期时间" width="200">
                <template slot-scope="scope">
                    <i class="el-icon-time"/>
                    <span>{{ scope.row.display_time }}</span>
                </template>
            </el-table-column>
        </el-table>
        <pagination v-show="total>0" :total="total" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getListt" />
    </div>
</template>

<script>
    import { getList } from '@/api/test_table1'
    import waves from '@/directive/waves'
    import Pagination from '@/components/Pagination' //分页 Secondary package based on el-pagination


    export default {
        components: { Pagination },
        directives: { waves },
        filters: {
            statusFilter(status) {
                const statusMap = {
                    已拥有: 'success',
                    未拥有: 'gray',
                    已过期: 'danger'
                }
                return statusMap[status]
            }
        },
        data() {
            return {
                currentPage:1,
                pageSize:20,
                list: null,
                listLoading: true,
                total: 0, //分页相关
                statusOptions: ["已拥有","未拥有","已过期"],
                listQuery: {
                    page: 1,
                    limit: 20,
                    importance: 1,
                    title: 1,
                    type: 1,
                    sort: '+id'
                },
                temp: {
                    id: 1,
                    importance: 1,
                    remark: '',
                    timestamp: new Date(),
                    title: '',
                    type: '',
                    status: '已拥有'
                },
            }
        },
        
        created() {
            this.fetchData()
        },
        methods: {
            getListt() {
                this.listLoading = true
                getList(this.listQuery).then(response => {
                    this.list = response.data.items
                    this.total = response.data.total
                    console.log(response.data.items)
                    // Just to simulate the time of the request
                    setTimeout(() => {
                        this.listLoading = false
                    }, 1.5 * 1000)
                })
            },
            fetchData() {
                this.listLoading = true
                getList(this.listQuery).then(response => {
                    this.list = response.data.items
                    this.total = response.data.total  //分页相关
                    this.listLoading = false


                })
            },
            handleFilter() {
                this.listQuery.page = 1
                this.getList()
            },
            handleModifyStatus(row, status) {
                this.$message({
                    message: '操作成功',
                    type: 'success'
                })
                row.status = status
            },
            sortChange(data) {
                const { prop, order } = data
                if (prop === 'id') {
                    this.sortByID(order)
                }
            },
        }
    }
</script>