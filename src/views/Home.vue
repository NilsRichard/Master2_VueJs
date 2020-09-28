<template>
  <div class="home" style="height:100%">
    <Todos />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { Background } from "../directives/background.directive";
import Todos from "@/components/Todos.vue";
@Component({
  components: {
    Todos,
  },
  directives: {
    Background,
  },
})
export default class Home extends Vue {
  message = "test";
  visible = true;
  foo() {
    this.visible = !this.visible;
  }
}
</script>