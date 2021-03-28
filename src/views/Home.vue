<template>
  <div class="h-screen pb-14">
    <EzCardTest />
  </div>
</template>

<script>
// @ is an alias to /src
import EzCardTest from "@/components/EzCardTest.vue";

export default {
  name: "Home",
  components: {
    EzCardTest,
  },
};
</script>
