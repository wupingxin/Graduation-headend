<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>学生管理</el-breadcrumb-item>
      <el-breadcrumb-item>来访列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区 -->
    <el-card>
      <!-- 添加板块 -->
      <el-form ref="addFormRef" label-width="60px">
        <el-row type="flex" justify="end">
          <el-col :span="3">
            <el-button
              type="primary"
              icon="el-icon-plus"
              @click="addDialogVisible = true"
              >添加</el-button
            >
          </el-col>
        </el-row>
      </el-form>
      <!-- 来访人员列表表格数据 -->
      <el-table :data="visitList" stripe border max-height="500px">
        <el-table-column type="index" width="47px"></el-table-column>
        <el-table-column
          label="来访人姓名"
          prop="visitor"
          width="100px"
        ></el-table-column>
        <el-table-column
          label="联系方式"
          prop="tel"
          width="120px"
        ></el-table-column>
        <el-table-column
          label="学生姓名"
          prop="name"
          width="100px"
        ></el-table-column>
        <el-table-column
          label="楼宇号"
          prop="lynumber"
          width="100px"
        ></el-table-column>
        <el-table-column
          label="宿舍号"
          prop="sushe"
          width="90px"
        ></el-table-column>
        <el-table-column
          label="来访时间"
          prop="comeTime"
          width="160px"
        ></el-table-column>
        <el-table-column
          label="离开时间"
          prop="leaveTime"
          width="160px"
        ></el-table-column>
        <el-table-column label="操作"  width="120px">
          <template slot-scope="scope">
            <!-- 删除 -->
            <el-tooltip
              effect="dark"
              content="删除"
              placement="top"
              :enterable="false"
            >
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="mini"
                @click="removeUserById(scope.row.id)"
              ></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[1, 3, 5, 7]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
      <!-- 添加用户的对话框 -->
      <el-dialog
        title="添加"
        :visible.sync="addDialogVisible"
        width="50%"
        @close="addDialogClosed"
      >
        <!-- 内容主体区 -->
        <el-form
          :model="addForm"
          :rules="addFormRules"
          ref="addFormRef"
          label-width="100px"
        >
          <el-form-item label="来访人姓名" prop="visitor">
            <el-input v-model="addForm.visitor"></el-input>
          </el-form-item>
          <el-form-item label="联系方式" prop="tel">
            <el-input v-model="addForm.tel"></el-input>
          </el-form-item>
          <el-form-item label="学生姓名" prop="name">
            <el-input v-model="addForm.name"></el-input>
          </el-form-item>
          <el-form-item label="楼宇号" prop="lynumber">
            <el-input v-model="addForm.lynumber"></el-input>
          </el-form-item>
          <el-form-item label="宿舍号" prop="sushe">
            <el-input v-model="addForm.sushe"></el-input>
          </el-form-item>
          <el-form-item label="来访时间" prop="comeTime">
            <el-input v-model="addForm.comeTime"></el-input>
          </el-form-item>
          <el-form-item label="离开时间" prop="leaveTime">
            <el-input v-model="addForm.leaveTime"></el-input>
          </el-form-item>
        </el-form>
        <!-- 底部按钮区 -->
        <span slot="footer" class="dialog-footer">
          <el-button @click="addDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addUser">确 定</el-button>
        </span>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    // 添加手机号的验证规则
    var checkPhone = (rule, value, cb) => {
      // 验证手机号的正则表达式
      // const regPhone = /^(0|86|17951)?(13[0-9]|15[0123456789]|17[678]|18[0-9]|14[57])[0-9]{8}$/
      const regPhone = /^[1][3,4,5,7,8][0-9]{9}$/;
      if (regPhone.test(value)) {
        return cb();
      }
      cb(new Error("请输入合法的手机号"));
    };
    return {
      // 添加来访人员的表单数据
      addForm: {
        visitor: "",
        tel: "",
        name: "",
        lynumber: "",
        sushe: "",
        comeTime: "",
        leaveTime: "",
      },
      // 添加表单的验证规则对象
      addFormRules: {
        visitor: [
          { required: true, message: "请输入来访人姓名", trigger: "blur" },
          {
            min: 2,
            max: 10,
            message: "长度在 2 到 10 个字符",
            trigger: "blur",
          },
        ],
        tel: [
          { required: true, message: "请输入联系电话", trigger: "blur" },
          { validator: checkPhone, trigger: "blur" }
        ],
        name: [
          { required: true, message: "请输入学生姓名", trigger: "blur" },
          {
            min: 2,
            max: 10,
            message: "长度在 2 到 10 个字符",
            trigger: "blur",
          },
        ],
        lynumber: [
          {
            required: true,
            message: "请填写楼宇号,格式为“x区x号楼”",
            trigger: "blur",
          },
        ],

      },
      // 来访人员信息列表
      visitList: [],
      // 分页表单数据
      queryInfo: {
        // 搜索关键字
        query: "",
        // 当前页数
        pagenum: 1,
        // 当前页每页显示多少条数据
        pagesize: 5,
      },
      // 总页数
      total: 0,
      // 控制添加用户的对话框的显示与隐藏
      addDialogVisible: false,
    };
  },
  created() {
    this.oneQuest();
  },
  methods: {
    // 封装页面加载请求函数
    oneQuest() {
      var _this = this;
      this.$http
        .post("http://localhost:3000/lflb/search", this.queryInfo)
        .then(function(response) {
          console.log(response.data);
          if (response.data.code == 200) {
            _this.visitList = response.data.data;
            _this.total = response.data.total;
          }
        });
    },
    // 监听pageSize改变的事件
    handleSizeChange(newSize) {
      console.log(newSize);
      this.queryInfo.pagesize = newSize;
      this.oneQuest();
    },
    // 监听页码值改变的事件
    handleCurrentChange(newPage) {
      console.log(newPage);
      this.queryInfo.pagenum = newPage;
      this.oneQuest();
    },
    // 监听添加来访人员对话框的关闭事件
    addDialogClosed() {
      // this.$refs.addFormRef.resetFields();
    },
    //点击确定按钮，添加人员
    addUser() {
      var _this = this;
      this.$refs.addFormRef.validate((valid) => {
        console.log(valid);
        if (!valid) return;
        // 如果预校验成功则发起添加请求
        // this.studentlist.unshift(this.addForm);
        this.$http
          .post("http://localhost:3000/lflb/add", this.addForm)
          .then(function(response) {
            console.log(response.data);
            if (response.data.code == 200) {
              _this.oneQuest();
              _this.$message({
                showClose: true,
                message: "添加成功",
                type: "success",
              });
            } else {
              _this.$message({
                showClose: true,
                message: "添加失败",
                type: "error",
              });
            }
          });
        this.addDialogVisible = false;
      });
    },
    // 删除事件
    async removeUserById(id) {
      var _this = this;
      // 弹框询问用户是否删除数据
      const cofirmResult = await this.$confirm(
        "此操作将永久删除该用户, 是否继续?",
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).catch((err) => err);
      // 如果用户确认删除，则返回字符串 confirm
      // 如果取消删除，则返回字符串 cancel
      // console.log(cofirmResult)
      if (cofirmResult !== "confirm") {
        return this.$message({
          showClose: true,
          message: "已取消删除",
          type: "info",
        });
      }
      // 根据id值发起请求删除该事件
      this.$http
        .post("http://localhost:3000/lflb/del", {
          id: id,
        })
        .then(function(response) {
          console.log(response.data);
          if (response.data.code == 200) {
            _this.oneQuest();
            return _this.$message({
              showClose: true,
              message: "删除成功",
              type: "success",
            });
          }
        });
    },
  },
};
</script>

<style lang="css" scoped>
.el-button {
  margin-left: 15px;
}
.el-table .success-row {
  background: #f0f9eb;
}
</style>
