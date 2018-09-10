<template>
    <div class="table"  v-loading="addloading">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i>客户信息</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="addClient">新增客户</el-button>
              
                <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="search" @click="search">搜索</el-button>
            </div>
            <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column prop="id" label="客户id"  width="100">
                </el-table-column>
                <el-table-column prop="clientName" label="客户名字"  width="150">
                </el-table-column>
                <el-table-column prop="clientCountry" label="国家" width="150">
                </el-table-column>
                <el-table-column prop="clientCity" label="城市" width="100">
                </el-table-column>
                  <el-table-column prop="clientIntention" label="客户意向"  width="250">
                </el-table-column>
                  <el-table-column prop="clientIntentionMachine" label="咨询机型" >
                </el-table-column>
                <el-table-column label="操作" width="180">
                    <template slot-scope="scope">
                        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :page-count="all_pages">
                </el-pagination>
            </div>
        </div>
       <!-- 新增弹出框 -->
        <el-dialog title="新增" :visible.sync="addVisible" width="30%"  >
            <el-form ref="form" :model="addform" label-width="100px" >
                <el-form-item label="姓名">
                      <el-input  v-model="addform.name"></el-input>
                </el-form-item>
                <el-form-item label="客户意向">
                  <el-select v-model="addform.intention" placeholder="请选择">
                    <el-option
                      v-for="item in options"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="国家">
                    <el-input v-model="addform.country"></el-input>
                </el-form-item>
                   <el-form-item label="城市">
                    <el-input v-model="addform.city"></el-input>
                </el-form-item>
                   <el-form-item label="咨询的机器">
                    <el-input placeholder="机器型号，以 , 分割，例如 R1,R2,R3"  type="textarea"  :autosize="{ minRows: 2, maxRows: 4}" v-model="addform.machine"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="addVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveClient">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="100px">
                <el-form-item label="姓名">
                      <el-input  v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="客户意向">
                  <el-select v-model="form.intention" placeholder="请选择">
                    <el-option
                      v-for="item in options"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="国家">
                    <el-input v-model="form.country"></el-input>
                </el-form-item>
                   <el-form-item label="城市">
                    <el-input v-model="form.city"></el-input>
                </el-form-item>
                   <el-form-item label="咨询的机器">
                    <el-input placeholder="机器型号，以 , 分割，例如 R1,R2,R3"  type="textarea"  :autosize="{ minRows: 2, maxRows: 4}" v-model="form.machine"></el-input>
                </el-form-item>

            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 删除提示框 -->
        <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false">取 消</el-button>
                <el-button type="primary" @click="deleteRow">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
export default {
  name: "basetable",
  data() {
    return {
      url: "./static/vuetable.json",
      tableData: [],
      cur_page: 1,
      all_pages: 1,
      multipleSelection: [],
      select_cate: "",
      select_word: "",
      del_list: [],
      editVisible: false,
      delVisible: false,
      addVisible: false,
      addloading: false,
      form: {
        id: "",
        name: "",
        intention: "",
        country: "",
        city: "",
        machine: ""
      },
      addform: {
        name: "",
        intention: "",
        country: "",
        city: "",
        machine: ""
      },
      options: [
        {
          value: "无进展客户",
          label: "无进展客户"
        },
        {
          value: "询价客户",
          label: "询价客户"
        },
        {
          value: "有意向客户",
          label: "有意向客户"
        },
        {
          value: "要来中国客户",
          label: "要来中国客户"
        },
        {
          value: "下单客户",
          label: "下单客户"
        },
        {
          value: "已成交客户",
          label: "已成交客户"
        }
      ],
      idx: -1,
      size: 10
    };
  },
  mounted() {
    this.getData();
  },

  methods: {
    // 分页导航
    handleCurrentChange(val) {
      this.cur_page = val;
      this.getData();
    },
    // 获取 easy-mock 的模拟数据
    getData() {
      this.addloading = true;
      var _this = this;
      var url = this.API.BaseUrl + "/client/list";
      var qs = require("qs");
      var data = qs.stringify({
        page: this.cur_page,
        size: this.size,
        key: this.select_word
      });
      this.$axios({
        method: "post",
        url: url,
        data
      })
        .then(res => {
          _this.tableData = res.data.data.list;
          _this.addloading = false;
          _this.all_pages = res.data.data.pages;
        })
        .catch(res => {
          _this.addloading = false;
          _this.$message.error(res);
        });
    },
    search() {
      this.cur_page = 1;
      this.getData();
    },
    filterTag(value, row) {
      return row.tag === value;
    },
    handleEdit(index, row) {
      this.idx = index;
      const item = this.tableData[index];
      this.form = {
        id: item.id,
        name: item.clientName,
        intention: item.clientIntention,
        country: item.clientCountry,
        city: item.clientCity,
        machine: item.clientIntentionMachine
      };
      this.editVisible = true;
    },
    handleDelete(index, row) {
      this.idx = index;
      this.delVisible = true;
    },
    addClient() {
      this.addVisible = !this.addVisible;
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    //新增客户
    saveClient(result) {
      this.addloading = true;
      if (
        this.addform.name == "" ||
        this.addform.intention == "" ||
        this.addform.country == "" ||
        this.addform.city == "" ||
        this.addform.machine == ""
      ) {
        this.$message.error("内容需要填写完整");
        return;
      }
      var url = this.API.BaseUrl + "/client/add";
      var _this = this;

      var qs = require("qs");
      var data = qs.stringify({
        clientName: this.addform.name,
        clientCountry: this.addform.country,
        clientCity: this.addform.city,
        clientIntention: this.addform.intention,
        clientIntentionMachine: this.addform.machine,
        closeTime: Math.round(new Date() / 1000)
      });
      this.$axios({
        method: "post",
        url: url,
        data
      })
        .then(res => {
          this.addloading = false;
          this.addVisible = false;
          this.$message.success(res.data.message);
        })
        .catch(res => {
          this.addloading = false;
          this.addVisible = false;
          this.$message.error(res);
        });
    },
    // 保存编辑
    saveEdit() {
      this.editVisible = true;
      if (
        this.form.name == "" ||
        this.form.intention == "" ||
        this.form.country == "" ||
        this.form.city == "" ||
        this.form.machine == ""
      ) {
        this.$message.error("内容需要填写完整");
        return;
      }
      var url = this.API.BaseUrl + "/client/update";
      var _this = this;
      var qs = require("qs");
      var data = qs.stringify({
        id: this.form.id,
        clientName: this.form.name,
        clientCountry: this.form.country,
        clientCity: this.form.city,
        clientIntention: this.form.intention,
        clientIntentionMachine: this.form.machine,
        closeTime: Math.round(new Date() / 1000)
      });
      this.$axios({
        method: "post",
        url: url,
        data
      })
        .then(res => {
          this.addloading = false;
          this.editVisible = false;
          this.$message.success(res.data.message);
          var newfrom = {
            id: this.form.name,
            clientName: this.form.name,
            clientCountry: this.form.country,
            clientCity: this.form.city,
            clientIntention: this.form.intention,
            clientIntentionMachine: this.form.machine
          };
          this.$set(this.tableData, this.idx, newfrom);
        })
        .catch(res => {
          this.addloading = false;
          this.editVisible = false;
          this.$message.error(res);
        });
    },
    // 确定删除
    deleteRow() {
      var newFrom = this.tableData[this.idx];
      console.log(newFrom)
      var url = this.API.BaseUrl + "/client/update";
      var _this = this;

      var qs = require("qs");
      var data = qs.stringify({
        id: newFrom.id,
        isDel: "1",
        closeTime: Math.round(new Date() / 1000)
      });
      this.$axios({
        method: "post",
        url: url,
        data
      })
        .then(res => {
          this.addloading = false;
          this.$message.success(res.data.message);
          this.tableData.splice(this.idx, 1);
        })
        .catch(res => {
          this.addloading = false;
          this.$message.error(res);
        });
      this.delVisible = false;
    }
  }
};
</script>

<style scoped>
.handle-box {
  margin-bottom: 20px;
}

.handle-select {
  width: 120px;
}

.handle-input {
  width: 400px;
  display: inline-block;
}
.del-dialog-cnt {
  font-size: 16px;
  text-align: center;
}
</style>
