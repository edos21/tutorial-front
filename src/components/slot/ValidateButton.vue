<template>
  <button @click="saveTutorial" class="btn btn-success">
    <slot/>
  </button>
</template>

<script>
import TutorialDataService from "@/services/TutorialDataService";

export default {
  name: "ValidateButton.vue",
  props:{},
  methods:{
    checkForm(){
      const regex = new RegExp('^(https?:\\/\\/)?'+ // protocol
          '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|'+ // domain name
          '((\\d{1,3}\\.){3}\\d{1,3}))'+ // OR ip (v4) address
          '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*'+ // port and path
          '(\\?[;&a-z\\d%_.~+=-]*)?'+ // query string
          '(\\#[-a-z\\d_]*)?$','i');
      const isValid = regex.test(this.tutorial.video_url)
      if (this.tutorial.title && isValid){
        return true;
      }
      this.errors = [];
      if (!this.tutorial.title){
        this.errors.push("Titulo obligatorio.")
      }
      if (!isValid){
        this.errors.push("URL invalida.")
      }
    },
    saveTutorial() {
      if (this.checkForm()) {
        const data = {
          title: this.tutorial.title,
          description: this.tutorial.description,
          video_url: this.tutorial.video_url,
          published_status: this.tutorial.published_status
        };
        TutorialDataService.create(data)
            .then(response => {
              this.tutorial.id = response.data.id;
              console.log(response.data);
              this.submitted = true;
            })
            .catch(e => {
              console.log(e);
            });
      }
    }
  }
}


</script>

<style scoped>

</style>