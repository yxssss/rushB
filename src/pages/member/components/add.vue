<template>
  <div>
    <el-dialog title="编辑会员" :visible.sync="info.isshow">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="手機號">
          <el-input v-model="form.phone"></el-input>
        </el-form-item>
         <el-form-item label="暱稱">
          <el-input v-model="form.nickname"></el-input>
        </el-form-item>
             <el-form-item label="密碼">
          <el-input v-model="form.nickname"></el-input>
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
        <el-button type="primary" @click="update">修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { warningAlert, successAlert } from "../../../utils/alert";
import { reqMemberDetail,reqMemberUpdate} from '../../../utils/request'
export default {
  props: ['info'],
  components: {},
  data() {
    return {
        form:{
            uid:'',
            nickname:'',
            phone:'',
            passwrod:'',
            status:1
        }
    };
  },
  computed: {
    ...mapGetters({
        list:'member/list'
    }),
  },
  methods: {
    ...mapActions({
         reqMemberAction: "member/reqListAction",
    }),
    empty(){
        this.form={
               uid:'',
            nickname:'',
            phone:'',
            passwrod:'',
            status:1
        }
        
    },
    look(uid){
        reqMemberDetail(uid).then(res=>{
              if (res.data.code == 200) {
          this.form = res.data.list;
        } else {
          warningAlert(res.data.msg);
        }
        })

    },
      cancel() {
      this.info.isshow = false;
    },
    update(){
        reqMemberUpdate(this.form).then(res=>{
             if (res.data.code == 200) {
          successAlert(res.data.msg);
          this.empty();
          this.cancel();
          this.reqMemberAction();
        } else {
          warningAlert(res.data.msg);
        }
        })
    }

  },
  mounted() {},
};
</script>
<style scoped>
</style>