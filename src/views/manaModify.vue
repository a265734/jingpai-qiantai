<template>
  <div class="wrap">
    <!-- main begin-->
    <div class="sale">
      <h1 class="lf">在线拍卖系统</h1>
      <div class="logout right">
       <a href="javaScript:void(0)" title="注销" @click="loginOut">注销</a>
      </div>
    </div>
    <div class="login logns produce">
      <h1 class="blues">拍卖品信息</h1>
      <el-form
        :model="shop"
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
        size="mini"
      >
        <el-form-item label="竞拍物品名称" label-width="120px" prop="shopname">
          <el-input v-model="shop.shopname"></el-input>
        </el-form-item>
        <el-form-item label="是否上线竞拍" label-width="120px" prop="stateid">
          <el-select v-model="shop.stateid" placeholder="请选择商品状态">
            <el-option label="上线" value="1"></el-option>
            <el-option label="下线" value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="起拍价" label-width="120px" prop="startmoney">
          <el-input v-model="shop.startmoney"></el-input>
        </el-form-item>
        <el-form-item label="成交价" label-width="120px">
          <el-input v-model="shop.endmoney"></el-input>
        </el-form-item>
        <el-form-item label="竞拍获得者" label-width="120px">
          <el-input v-model="shop.endname"></el-input>
        </el-form-item>
        <el-form-item label="物品描述" label-width="120px">
          <el-input v-model="shop.shopdesc"></el-input>
        </el-form-item>
        <el-form-item label="物品图片" label-width="120px" prop="imgpath">
          <el-upload
            action
            :on-change="getFile"
            :limit="1"
            list-type="picture"
            :auto-upload="false"
          >
            <el-button size="small" type="primary">选择图片上传,最多上传一张图片</el-button>
            <!-- <ul class="el-upload-list el-upload-list--picture" id="img" v-if="shop.imgpath!=''">
              <li class="el-upload-list__item is-ready el-list-enter-to" tabindex="0">
                <img :src="shop.imgpath" id="img" class="el-upload-list__item-thumbnail" />
              </li>
            </ul> -->
          </el-upload>
        </el-form-item>

        <el-form-item label="竞拍时间" label-width="120px" required>
          <el-date-picker
            v-model="timeVal"
            type="datetimerange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
           :picker-options="pickerOptions0"></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">保存</el-button>
        </el-form-item>
      </el-form>
    </div>
    <!-- main end-->
    <!-- footer begin-->
  </div>
  <!--footer end-->
</template>

<script>
export default {
  data() {
    //这里存放数据
    return {
        timeVal:[],
        pickerOptions0: {
          disabledDate(time) {
            return time.getTime() < Date.now() - 8.64e7;//如果没有后面的-8.64e6就是不可以选择今天的
          }
        },
      shop:{
          userid:this.$store.getters.getUser.userid,
          shopid:"",
          shopname:"",
          startmoney:"",
          endmoney:"",
          endname:"",
          starttime:"",
          endtime:"",
          imgpath:"",
          shopdesc:"",
          stateid:"",
      },
      rules: {
          shopname: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
          ],
          startmoney: [
            { required: true, message: '请输入起拍价', trigger: 'blur' }
          ],
          imgpath: [
            { required: true, message: '请选择图片', trigger: 'change' }
          ],
          stateid: [
            { required: true, message: '请选择物品状态', trigger: 'change' }
          ]
        }
    };
  },
  //计算属性
  computed: {},
  //方法集合
  methods: {
      loginOut() {
      this.$store.commit("setUser", null);
      this.$router.push("/");
    },
      submitForm(formName) {
        let _this=this;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            _this.shop.starttime=this.timeVal[0];
            _this.shop.endtime=this.timeVal[1];
            _this.addShop();
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      addShop(){
        let _this=this;
        console.log(_this.shop);
        this.$jq.ajax(`http://127.0.0.1:8080/api/shops/shop/add`, {
          type: 'post',
          data: JSON.stringify(_this.shop),
          contentType: "application/json",
          success(data) {
              if(data.code=="200"){
                _this.$message({
                    message: '新增成功',
                    type: 'success'
                });
                _this.$router.push("/manaList");
              }else{
                _this.$message({
                    message: '新增失败',
                    type: 'error'
                });
              }
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
      getBase64(file) {
      return new Promise(function(resolve, reject) {
        let reader = new FileReader();
        let imgResult = "";
        reader.readAsDataURL(file);
        reader.onload = function() {
          imgResult = reader.result;
        };
        reader.onerror = function(error) {
          reject(error);
        };
        reader.onloadend = function() {
          resolve(imgResult);
        };
      });
    },
    getFile(file, fileList) {
      let _this=this;
      this.$jq("#img").hide();
      this.getBase64(file.raw).then(res => {
        console.log(res)
        _this.shop.imgpath=res;
      });
    },
  },
  //挂载完成（可以访问DOM元素）
  mounted() {},
  //监控data中的数据变化
  watch: {}
};
</script>
<style  scoped>
/*@import url(); 引入公共css类*/
@import url("../assets/css/common.css");
@import url("../assets/css/style.css");
</style>