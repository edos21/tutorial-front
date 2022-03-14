<template>
  <div v-if="currentTutorial" class="edit-form">
    <h4>Tutorial</h4>
    <div v-if="errors.length">
      <b>Es necesario corregir los siguientes errores:</b>
      <ul>
        <li class="text-danger" v-for="error in errors" :key="error">{{ error }}</li>
      </ul>
    </div>
    <form>
      <div class="form-group">
        <label for="title">Titulo</label>
        <input type="text" class="form-control" id="title"
               v-model="currentTutorial.title"
        />
      </div>
      <div class="form-group">
        <label><strong>URL:</strong></label>
        <input type="text" class="form-control" ids="video_url"
               v-model="currentTutorial.video_url"
        />
      </div>
      <div class="form-group">
        <label for="description">Descripci&oacute;n</label>
        <textarea type="text" class="form-control" id="description"
               v-model="currentTutorial.description"
        />
      </div>
    </form>
    <button class="btn btn-primary mr-2 mb-3"
            v-if="currentTutorial.published_status"
            @click="updatePublished(false)">
      Ocultar
    </button>
    <button v-else class="btn btn-primary mr-2 mb-3"
            @click="updatePublished(true)">
    Publicar
    </button>
    <button class="btn btn-danger mr-2 mb-3"
            @click="deleteTutorial">
      Borrar
    </button>
    <button type="submit" class="btn btn-success mb-3"
            @click="updateTutorial"
    >
      Actualizar
    </button>
    <p>{{ message }}</p>
  </div>
  <div v-else>
    <br />
    <p>Seleccionar un tutorial para ver su contenido...</p>
  </div>
</template>
<script>
import TutorialDataService from "../services/TutorialDataService";
export default {
  name: "TutorialRead",
  data() {
    return {
      currentTutorial: null,
      message: '',
      errors: []
    };
  },
  methods: {
    checkForm(){
      const regex = new RegExp('^(https?:\\/\\/)?'+ // protocol
          '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|'+ // domain name
          '((\\d{1,3}\\.){3}\\d{1,3}))'+ // OR ip (v4) address
          '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*'+ // port and path
          '(\\?[;&a-z\\d%_.~+=-]*)?'+ // query string
          '(\\#[-a-z\\d_]*)?$','i');
      const isValid = regex.test(this.currentTutorial.video_url)
      if (this.currentTutorial.title && isValid){
        return true;
      }
      this.errors = [];
      if (!this.currentTutorial.title){
        this.errors.push("Titulo obligatorio.")
      }
      if (!isValid){
        this.errors.push("URL invalida.")
      }
    },
    getTutorial(id) {
      TutorialDataService.get(id)
          .then(response => {
            this.currentTutorial = response.data;
            console.log(response.data);
          })
          .catch(e => {
            console.log(e);
          });
    },
    updatePublished(status) {
      const data = {
          published_status: status
      };
      TutorialDataService.update(this.currentTutorial.id, data)
      .then(response => {
        console.log(response.data);
        this.currentTutorial.published_status = status;
        this.message = 'El estado fue modificado exitosamente!';
        setTimeout(()=> this.$router.push({ name: "tutorials" }), 2000)
        })
      .catch(e => {
        console.log(e);
        });
      },
    updateTutorial() {
      if (this.checkForm()) {
        TutorialDataService.update(this.currentTutorial.id, this.currentTutorial)
            .then(response => {
              console.log(response.data);
              this.message = 'Tutorial modificado con exito!';
              setTimeout(()=> this.$router.push({ name: "tutorials" }), 3000)
            })
            .catch(e => {
              console.log(e);
            });
      }
    },
    deleteTutorial() {
      TutorialDataService.delete(this.currentTutorial.id)
          .then(response => {
            console.log(response.data);
            this.$router.push({ name: "tutorials" });
          })
          .catch(e => {
            console.log(e);
          });
    }
  },
  mounted() {
    this.message = '';
    this.getTutorial(this.$route.params.id);
  }
};
</script>
<style>
.edit-form {
  max-width: 600px;
  border-radius: 5px;
  padding: 20px;
  margin: auto;
  background: #cdd5dd;
}
.block-input {
  display: block;
}
.card{
  background: none;
  border: none;
}
</style>