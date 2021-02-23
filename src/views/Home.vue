<template>
  <div>
    <div class="header"></div>
    <div class="menu">
      <h4>学生管理系统</h4>

      <ul>
        <li :class="{ active: activeMenu == 1 }" @click="activeMenu = 1">
          学生管理
        </li>
      </ul>
    </div>
    <div class="content">
      <el-button type="primary" size="small" @click="clickToAddStudent"
        >新增学生</el-button
      >
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="id" label="id" width="80"> </el-table-column>
        <el-table-column prop="name" label="姓名" width="180">
        </el-table-column>
        <el-table-column prop="address" label="地址"> </el-table-column>
        <el-table-column label="操作" width="80" fixed="right">
          <template slot-scope="scope">
            <el-button
              type="text"
              size="small"
              @click="clickToEditStudent(scope.row)"
              >编辑</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </div>

    <el-dialog title="提示" :visible.sync="dialogVisible" width="40%">
      <el-form ref="form" :model="form" :rules="formRules">
        <el-form-item label="学生名称" prop="name">
          <el-input v-model="form.name" autocomplete="off" clearable></el-input>
        </el-form-item>
        <el-form-item label="学生地址">
          <el-input
            v-model="form.address"
            autocomplete="off"
            clearable
          ></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="save"> 保 存 </el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data: () => {
    return {
      tableData: [],
      activeMenu: 1,
      form: {
        name: "",
        address: "",
      },
      formRules: {
        name: [{ required: true, message: "请输入学生名称", trigger: "blur" }],
      },
      dialogVisible: false,
      api:
        "https://mock.mengxuegu.com/mock/6035050cca363222f3d5791b/example/student/list",
    };
  },
  created() {
    this.getData();
  },
  methods: {
    getData() {
      this.$axios
        .get(this.api)
        .then((res) => {
          this.tableData = res.data.data || [];
        })
        .catch((err) => {
          console.log(err);
        });
    },
    // 新增
    clickToAddStudent() {
      this.form = {
        name: "",
        address: "",
      };
      this.dialogVisible = true;
    },
    // 编辑
    clickToEditStudent(item) {
      this.form = item;
      this.dialogVisible = true;
    },

    // 保存
    save() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          let post = this.form;
          let fun = post.id ? this.$axios.put : this.$axios.post;

          fun(this.api, post).then((res) => {
            if (res.data.data == "success") {
              this.$message.success("保存成功");
              this.dialogVisible = false;
              this.getData();
            }
          });
        }
      });
    },
  },
};
</script>

<style scoped lang="scss">
.container {
  height: 100%;
  border: 1px solid #eee;
}

.header {
  position: absolute;
  top: 0;
  left: 300px;
  right: 0;
  height: 50px;
  line-height: 50px;
  background-color: rgb(179, 192, 209);
}

.menu {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  width: 300px;
  background-color: rgb(121, 121, 121);

  h4 {
    margin: 0;
    padding: 14px 0;
    color: white;
    border-bottom: 1px solid #bbb;
    text-align: center;
  }

  ul {
    padding: 0;
    margin: 0;
  }

  li {
    list-style: none;
    padding: 10px 30px;
    text-align: left;
    cursor: pointer;

    &.active,
    &:hover {
      background-color: #fff;
    }
  }
}

.content {
  position: absolute;
  top: 50px;
  left: 300px;
  right: 0;
  bottom: 20px;
  background-color: white;
  padding: 10px;
  overflow: auto;
}

.el-aside {
  color: #333;
}
</style>
