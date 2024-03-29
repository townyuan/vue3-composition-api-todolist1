<script setup>
defineProps({
  msg: {
    type: String,
    required: true
  }
})
</script>

<template>
  <div class="box">
    <h1>Hello World!!</h1>
  </div>
</template>

<style scoped>
.box {
  margin: 0 auto;
  padding: 100px 20px 20px;
  max-width: 800px;
  text-align: center;
}
</style>
