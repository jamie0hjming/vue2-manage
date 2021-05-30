<template>
  <el-table :data="tableData">
    <el-table-column type="expand">
      <template slot-scope="props">
        <el-form label-position="left" inline class="demo-table-expand">
          <el-row type="flex" class="row-bg" justify="space-around">
            <el-col :span="12">
              <el-form-item label="商品名称">
                <span>{{ props.row.name }}</span>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="食品ID">
                <span>{{ props.row.item_id }}</span>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row type="flex" class="row-bg" justify="space-around">
            <el-col :span="12">
              <el-form-item label="食品介绍">
                <span>{{ props.row.description }}</span>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="食品评分">
                <span>{{ props.row.rating }}</span>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row type="flex" class="row-bg" justify="space-around">
            <el-col :span="12">
              <el-form-item label="月销量">
                <span>{{ props.row.month_sales }}</span>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="餐馆名称">
                <span>{{ props.row.restaurant_id }}</span>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row type="flex" class="row-bg" justify="space-around">
            <el-col :span="12">
              <el-form-item label="餐馆ID">
                <span>{{ props.row.restaurant_id }}</span>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="餐馆地址">
                <span>{{ props.row.description }}</span>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row type="flex" class="row-bg" justify="space-around">
            <el-col :span="12">
              <el-form-item label="食品分类">
                <span>{{ props.row.category_id }}</span>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="餐馆地址">
                <span>{{ props.row.description }}</span>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column
      v-for="(item, index) in tableConfig1"
      :prop="item.prop"
      :label="item.label"
      :width="item.width"
      :key="index"
    >
    </el-table-column>
    <el-table-column fixed="right" label="操作" width="100">
      <template slot-scope="scope">
        <el-button type="text" size="small">查看</el-button>
        <el-button type="text" size="small">编辑</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
import { login, getFoods } from "@/api/getData";
import { mapActions, mapState } from "vuex";

export default {
  data() {
    return {
      loginForm: {
        username: "",
        password: "",
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
        ],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
      },
      showLogin: false,
      tableData: [],
      tableConfig: ["name", "address", "phone", "age"],
      tableConfig1: [
        {
          prop: "name",
          label: "食品名称",
        },
        {
          prop: "description",
          label: "食品描述",
        },
        {
          prop: "rating",
          label: "评分",
        },
      ],
    };
  },
  mounted() {
    this.getAjax();

    // let ff = []
    // this.tableConfig.forEach(element => {
    // 	let ee = {
    // 		prop: element,
    // 		label: element
    // 	}
    // 	ff.push(ee)
    // });
    // this.tableConfig1 = ff;

    // let gg = []
    // for (let w = 1; w < 15; w++) {
    // 	let hh ={}
    // 	this.tableConfig.forEach(ele => {
    // 		hh[ele] = parseInt(Math.random()*10);
    // 	})
    // 	gg.push(hh)
    // }
    // this.tableData = gg

    // let i = 200
    // let b = []
    // for (let index = 0; index < i; index++) {
    // 	const element = 'item' + index;
    // 	b.push({
    // 		prop: element,
    // 		label: element,
    // 		width: 100
    // 	})

    // }
    // this.tableConfig1 = b
    // let aa = []
    // for (let r = 0; r < 100; r++) {
    // 	let c =  {}
    // 	for (let index = 0; index < i; index++) {
    // 		c["item"+index]  = parseInt(Math.random() * 100)
    // 	}
    // 	this.tableData =  [c]
    // 	aa.push(c)
    // }
    // this.tableData = aa

    // this.showLogin = true;
    // if (!this.adminInfo.id) {
    // 	this.getAdminData()
    // }
  },
  computed: {
    ...mapState(["adminInfo"]),
  },
  methods: {
    ...mapActions(["getAdminData"]),

    async submitForm(formName) {
      this.$refs[formName].validate(async (valid) => {
        if (valid) {
          const res = await login({
            user_name: this.loginForm.username,
            password: this.loginForm.password,
          });
          if (res.status == 1) {
            this.$message({
              type: "success",
              message: "登录成功",
            });
            this.$router.push("manage");
          } else {
            this.$message({
              type: "error",
              message: res.message,
            });
          }
        } else {
          this.$notify.error({
            title: "错误",
            message: "请输入正确的用户名密码",
            offset: 100,
          });
          return false;
        }
      });
    },
    async getAjax() {
      const resData = await getFoods();
      console.log("resData", resData);
      this.tableData = resData;
    },
  },
  watch: {
    adminInfo: function (newValue) {
      if (newValue.id) {
        this.$message({
          type: "success",
          message: "检测到您之前登录过，将自动登录",
        });
        this.$router.push("manage");
      }
    },
  },
};
</script>

<style lang="less" scoped>
@import "../style/mixin";
.login_page {
  background-color: #324057;
}
// .demo-table-expand {
// 	font-size: 0;
// }
.demo-table-expand label {
	width: 90px;
	color: #99a9bf;
}
.demo-table-expand .el-form-item {
	margin-right: 0;
	margin-bottom: 0;
	width: 50%;
}
.manage_tip {
  position: absolute;
  width: 100%;
  top: -100px;
  left: 0;
  p {
    font-size: 34px;
    color: #fff;
  }
}
.form_contianer {
  .wh(320px, 210px);
  .ctp(320px, 210px);
  padding: 25px;
  border-radius: 5px;
  text-align: center;
  background-color: #fff;
  .submit_btn {
    width: 100%;
    font-size: 16px;
  }
}
.tip {
  font-size: 12px;
  color: red;
}
.form-fade-enter-active,
.form-fade-leave-active {
  transition: all 1s;
}
.form-fade-enter,
.form-fade-leave-active {
  transform: translate3d(0, -50px, 0);
  opacity: 0;
}
</style>
