<template>
  <button class="btn">
      <slot></slot>
  </button>
</template>

<script>
export default {
    name:'my-button'
}
</script>

<style scoped>
    .btn {
        display: block;
        width: 150px;
        height: 35px;
        margin: 0 auto;
        /* margin-top: 15px; */
        background: none;
        color: teal;
        border: 1px solid teal;
        cursor: pointer;    
    }
</style>