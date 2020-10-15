<template>
  <div>
    <h1>会员管理</h1>
    <v-add :info='info' ref="add"></v-add>
    <v-list @edit='edit' ></v-list>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";


import vAdd from './components/add'
import vList from './components/list'
export default {
  props: [],
  components: {
    vAdd,
    vList
  },
  data() {
    return {
      info:{isshow:false}
    };
  },
  computed: {
    ...mapGetters({}),
  },
  methods: {
    ...mapActions({}),
    // 编辑
    edit(uid){
      this.info.isshow = true
      this.$refs.add.look(uid)
    }
  },
  mounted() {
  },
};
</script>
<style scoped>
</style>