<template>
  <div id="app">
    <el-dialog :visible.sync="show"></el-dialog>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from '@/components/HelloWorld.vue'
import Dialog from '@/components/Dialog'

@Component({
  components:{
      'hello-world':HelloWorld,
      'el-dialog':Dialog
  }
})

export default class App extends Vue {
    show:Boolean = true;
}
</script>

<style lang="scss" scoped>
</style>
