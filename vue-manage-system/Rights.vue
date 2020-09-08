<template>
  <div>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card>
      <el-table :data="rightsList" border stripe>
        <el-table-column
          type="index"
          label="#"
          align="center"
        ></el-table-column>
        <el-table-column
          label="权限名称"
          prop="authName"
          align="center"
        ></el-table-column>
        <el-table-column
          label="路径"
          prop="path"
          align="center"
        ></el-table-column>
        <el-table-column label="权限等级" prop="level" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.level === '0'">一级</el-tag>
            <el-tag type="success" v-else-if="scope.row.level === '1'"
              >二级</el-tag
            >
            <el-tag type="warning" v-else>三级</el-tag>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <el-row>
      <el-col :span="24">
        <div class="pagination">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="paginations.page_index"
            :page-sizes="paginations.page_sizes"
            :page-size="paginations.page_size"
            :layout="paginations.layout"
            :total="paginations.total"
          >
          </el-pagination>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      paginations: {
        page_index: 1, //当期位于哪页
        total: 0, //总数
        page_size: 5, //一页显示多少条
        page_sizes: [5, 10, 15, 50],
        layout: "total, sizes, prev, pager, next, jumper" //翻页属性
      },
      //权限列表
      rightsList: [],
      allRightList: []
    };
  },
  created() {
    //获取所有的权限
    this.getRightsList();
  },
  methods: {
    //获取权限列表

    async getRightsList() {
      const { data: res } = await this.$http.get("rights/list"); //通过await返回的数据对象中结构出data
      if (res.meta.status !== 200) {
        return this.$message.error("获取权限列表失败");
      }
      // this.rightsList = res.data;
      //改成
      this.allRightList = res.data;
      //设置分页数据
      this.setPagination();
      // console.log(this.rightsList);
    },
    setPagination() {
      //分页属性设置
      this.paginations.total = this.allRightList.length;
      this.paginations.page_index = 1;
      this.paginations.page_size = 5;
      //设置默认的分页数据
      this.rightsList = this.allRightList.filter((item, index) => {
        return index < this.paginations.page_size;
      });
    },
    handleSizeChange(page_size) {
      //切换size
      this.paginations.page_index = 1;
      this.paginations.page_size = page_size;

      this.rightsList = this.allRightList.filter((item, index) => {
        return index < page_size;
      });
    },
    handleCurrentChange(page) {
      //获取当前页  de下标了
      let index = this.paginations.page_size * (page - 1);
      //数据的总数
      let nums = this.paginations.page_size * page;
      //容器
      let tableggs = [];
      for (let i = index; i < nums; i++) {
        if (this.allRightList) {
          tableggs.push(this.allRightList[i]);
        }
        this.rightsList = tableggs;
      }
    }
  }
};
</script>

<style scoped>
.pagination {
  margin-top: 10px;
  text-align: right;
}
</style>
