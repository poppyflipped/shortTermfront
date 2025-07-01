<template>
  <div class="home">
    <div class="header">
      <h2>学生学习情况登记系统</h2>
      <el-input v-model="searchKeyword" placeholder="输入姓名搜索" clearable class="search" @input="handleSearch" />
      <el-button type="primary" icon="el-icon-plus" @click="handleAdd">新增</el-button>
      <el-button type="danger" icon="el-icon-delete" @click="handleBatchDelete" :disabled="!multipleSelection.length">批量删除</el-button>
    </div>

    <el-table
      :data="filteredData"
      border
      style="width: 100%"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" />
      <el-table-column prop="rank" label="排名" width="80" />
      <el-table-column prop="name" label="姓名" width="120" />
      <el-table-column prop="studentId" label="学号" width="150" />
      <el-table-column prop="score" label="成绩" width="100" />
      <el-table-column prop="class" label="班级" width="120" />
      <el-table-column label="操作" width="180">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 新增/编辑对话框 -->
    <el-dialog :title="dialogTitle" :visible.sync="dialogVisible">
      <el-form :model="form" label-width="80px">
        <el-form-item label="姓名">
          <el-input v-model="form.name" />
        </el-form-item>
        <el-form-item label="学号">
          <el-input v-model="form.studentId" />
        </el-form-item>
        <el-form-item label="成绩">
          <el-input type="number" v-model.number="form.score" />
        </el-form-item>
        <el-form-item label="班级">
          <el-input v-model="form.class" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="handleSave">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [
        { rank: 1, name: '张三', studentId: '20230001', score: 98, class: '计算机1班' },
        { rank: 2, name: '李四', studentId: '20230002', score: 95, class: '计算机1班' },
        { rank: 3, name: '王五', studentId: '20230003', score: 90, class: '计算机2班' },
        { rank: 4, name: '赵六', studentId: '20230004', score: 88, class: '计算机2班' }
      ],
      searchKeyword: '',
      filteredData: [],
      dialogVisible: false,
      dialogTitle: '新增成绩',
      form: {
        name: '',
        studentId: '',
        score: null,
        class: ''
      },
      isEditing: false,
      editingIndex: null,
      multipleSelection: []
    }
  },
  created() {
    this.filteredData = [...this.tableData]
  },
  methods: {
    handleSearch() {
      const keyword = this.searchKeyword.trim().toLowerCase()
      this.filteredData = this.tableData.filter(item =>
        item.name.toLowerCase().includes(keyword)
      )
    },
    handleAdd() {
      this.dialogTitle = '新增成绩'
      this.dialogVisible = true
      this.isEditing = false
      this.form = {
        name: '',
        studentId: '',
        score: null,
        class: ''
      }
    },
    handleEdit(row) {
      this.dialogTitle = '编辑成绩'
      this.dialogVisible = true
      this.isEditing = true
      this.editingIndex = this.tableData.indexOf(row)
      this.form = Object.assign({}, row)
    },
    handleSave() {
      if (this.isEditing) {
        this.$set(this.tableData, this.editingIndex, { ...this.form })
      } else {
        this.tableData.push({ ...this.form })
      }
      this.dialogVisible = false
      this.updateRank()
      this.handleSearch()
    },
    handleDelete(row) {
      this.$confirm('确定要删除这条记录吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.tableData = this.tableData.filter(item => item !== row)
        this.updateRank()
        this.handleSearch()
      })
    },
    handleBatchDelete() {
      this.$confirm('确定要删除选中的记录吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.tableData = this.tableData.filter(item => !this.multipleSelection.includes(item))
        this.updateRank()
        this.handleSearch()
      })
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    updateRank() {
      this.tableData.sort((a, b) => b.score - a.score)
      this.tableData.forEach((item, index) => {
        item.rank = index + 1
      })
    }
  }
}
</script>

<style scoped>
.home {
  padding: 30px;
  background-color: #f9f9f9;
  min-height: 100vh;
}
.header {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}
.header h2 {
  flex: 1;
  margin: 0;
  font-size: 24px;
  color: #333;
}
.search {
  width: 250px;
  margin-right: 10px;
}
.dialog-footer {
  text-align: right;
}
</style>
