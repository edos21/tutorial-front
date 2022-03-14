<template>
  <h4>Tutorial</h4>
  <div>
    <label><strong>Titulo:</strong></label> {{ currentTutorial.title }}
  </div>
  <div>
    <iframe width="580" height="360"
            :src="currentTutorial.video_url">
    </iframe>
  </div>
  <div>
    <label><strong>Descripci&oacute;n:</strong></label> {{ currentTutorial.description }}
  </div>
  <div>
    <label><strong>Estado:</strong></label> {{ currentTutorial.published_status ? "Publicado" : "Pendiente" }}
  </div>
  <router-link :to="'/tutorials/' + currentTutorial.id" class="btn btn-warning">Edit</router-link>
</template>

<script>
export default {
  name: "DetailTutorial",
  props:{
    currentTutorial: Object,
  }
}
</script>

<style scoped>

</style>