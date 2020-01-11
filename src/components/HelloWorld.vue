<template>
  <div class="hello">
    <div><button @click="toggle">toggle</button></div>
    <div>{{visible}}</div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class HelloWorld extends Vue {
    @Prop() private visible:Boolean = false;
    private toggle(){
        this.$emit('update:visible',!this.visible)
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
