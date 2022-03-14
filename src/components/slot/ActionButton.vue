<template>
  <button :class="'btn btn-'+action">
    <slot/>
  </button>
</template>

<script>
export default {
  name: "ActionButton.vue",
  props:["action"],
}


</script>
