<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加活动' : '编辑活动'"
      :visible.sync="info.isshow"
      @closed="close"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="活动名称">
          <el-input v-model="form.title"></el-input>
        </el-form-item>

        <el-form-item label="活动时间">
          <el-date-picker
            v-model="time"
            @change="changeTime"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          >
          </el-date-picker>
        </el-form-item>

        <el-form-item label="一级分类">
          <el-select v-model="form.first_cateid" @change="changeFirst">
            <el-option label="请选择" value=""></el-option>
            <el-option
              v-for="item in cateList"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级分类">
          <el-select v-model="form.second_cateid" @change="changeSecond">
            <el-option label="请选择" value=""></el-option>
            <el-option
              v-for="item in secondList"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="商品">
          <el-select v-model="form.goodsid">
            <el-option label="请选择" value=""></el-option>
            <el-option
              v-for="item in goodsList"
              :key="item.id"
              :label="item.goodsname"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="状态">
          <el-switch
            v-model="form.status"
            :active-value="1"
            :inactive-value="2"
          ></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" @click="add" v-if="info.isAdd"
          >添 加</el-button
        >
        <el-button type="primary" @click="update" v-else>修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import {
  reqSeckAdd,
  reqSeckDetail,
  reqSeckUpdate,
  reqCateList,
  reqGoodsList,
} from "../../../utils/request";
import { warningAlert, successAlert } from "../../../utils/alert";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      time: [],
      secondList: [],
      goodsList: [],
      form: {
        title: "",
        begintime: "",
        first_cateid: "",
        endtime: "",
        second_cateid: "",
        goodsid: "",
        status: 1,
      },
    };
  },
  computed: {
    ...mapGetters({
      cateList: "cate/list",
    }),
  },
  methods: {
    ...mapActions({
      reqListAction: "seckill/reqListAction",
      reqCateListAction: "cate/reqListAction",
    }),
    changeTime(e) {
      console.log(e);
      this.form.begintime = e[0].getTime();
      this.form.endtime = e[1].getTime();
    },
    changeFirst() {
      this.form.second_cateid = "";
      this.form.goodsid = "";
      reqCateList({ pid: this.form.first_cateid }).then((res) => {
        this.secondList = res.data.list;
      });
    },
    changeSecond() {
      this.form.goodsid = "";
      reqGoodsList({
        fid: this.form.first_cateid,
        sid: this.form.second_cateid,
      }).then((res) => {
        this.goodsList = res.data.list;
      });
    },
    //取消
    cancel() {
      this.info.isshow = false;
    },
    //弹框消失完成
    close() {
      //如果是添加开的弹框，就什么都不做；如果是编辑开的弹框，就清除form
      if (!this.info.isAdd) {
        this.empty();
      }
    },
    //数据重置
    empty() {
      this.time = "";
      this.form = {
        title: "",
        begintime: "",
        first_cateid: "",
        endtime: "",
        second_cateid: "",
        goodsid: "",
        status: 1,
      };
    },
    //点击了添加按钮
    add() {
      // console.log(this.form);
      reqSeckAdd(this.form).then((res) => {
        if (res.data.code == 200) {
          //成功
          successAlert(res.data.msg);

          //数据重置
          this.empty();

          //弹框消失
          this.cancel();

          //list数据要刷新
          this.reqListAction();
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    //获取菜单详情 （1条）
    look(id) {
      //发请求
      reqSeckDetail(id).then((res) => {
        if (res.data.code == 200) {
          this.form = res.data.list;
          this.form.id = id;
          this.time.push(new Date(Number(this.form.begintime)));
          this.time.push(new Date(Number(this.form.endtime)));
          reqCateList({ pid: this.form.first_cateid }).then((res) => {
            this.secondList = res.data.list;
          });
          reqGoodsList({fid: this.form.first_cateid,sid: this.form.second_cateid,}).then((res) => {
            this.goodsList = res.data.list;
          });
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    //修改
    update() {
      reqSeckUpdate(this.form).then((res) => {
        if (res.data.code == 200) {
          successAlert(res.data.msg);
          this.empty();
          this.cancel();
          this.reqListAction();
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
  },
  mounted() {
    if (this.cateList.length == 0) {
      this.reqCateListAction();
    }
  },
};
</script>
<style scoped>
</style>