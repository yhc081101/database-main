// 学生管理页面
<template>

</template>

<script>
export default {
  data() {
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 10, //每页条数
      },
      dialogVisible: false, //对话框
      form: {}, //保存点击以后当前试卷的信息
    };
  },
  created() {
    this.getStudentInfo();
  },
  methods: {
    cellStyle({row,column,rowIndex,columnIndex}){
      return "text-align:center";
    },
    rowClass({row,rowIndex}){
      return "text-align:center";
    },
    getStudentInfo() {
      //分页查询所有试卷信息
      this.$axios(`/api/students/${this.pagination.current}/${this.pagination.size}`).then(res => {
        this.pagination = res.data.data;
      }).catch(error => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getStudentInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getStudentInfo();
    },
    checkGrade(studentId) { //修改学生信息
      this.dialogVisible = true
      this.$axios(`/api/student/${studentId}`).then(res => {
        this.form = res.data.data
      })
    },
    deleteById(studentId) { //删除当前学生
      this.$confirm("确定删除当前学生吗？删除后无法恢复","Warning",{
        confirmButtonText: '确定删除',
        cancelButtonText: '算了,留着吧',
        type: 'danger'
      }).then(()=> { //确认删除
        this.$axios({
          url: `/api/student/${studentId}`,
          method: 'delete',
        }).then(res => {
          this.getStudentInfo()
        })
      }).catch(() => {

      })
    },
    submit() { //提交更改
      this.dialogVisible = false
      this.$axios({
        url: '/api/student',
        method: 'put',
        data: {
          ...this.form
        }
      }).then(res => {
        console.log(res)
        if(res.data.code ==200) {
          this.$message({
            message: '更新成功',
            type: 'success'
          })
        }
        this.getStudentInfo()
      })
    },
    handleClose(done) { //关闭提醒
      this.$confirm('确认关闭？')
        .then(_ => {
          done();
        }).catch(_ => {});
    },
  }
};
</script>
<style lang="scss" scoped>
.all {
  padding: 0px 40px;
  .page {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .edit {
    margin-left: 20px;
  }
  .el-table tr {
    background-color: #dd5862 !important;
  }
}
.el-table .warning-row {
  background: #000 !important;
}

.el-table .success-row {
  background: #dd5862;
}
</style>
