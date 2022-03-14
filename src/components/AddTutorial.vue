<template>
  <div class="submit-form">
    <div v-if="!submitted">
      <div>
        <h4>Nuevo Tutorial</h4>
        <div v-if="errors.length">
          <b>Es necesario corregir los siguientes errores:</b>
        <ul>
          <li class="text-danger" v-for="error in errors" :key="error">{{ error }}</li>
        </ul>
        </div>
        <div class="form-group">
          <label for="title">Titulo</label>
          <input
              type="text"
              class="form-control"
              id="title"
              required
              v-model="tutorial.title"
              name="title"
          />
        </div>
        <div class="form-group">
          <label for="video_url">URL</label>
          <input
              type="url"
              class="form-control"
              id="video_url"
              required
              v-model="tutorial.video_url"
              name="video_url"
          />
        </div>
        <div class="form-group">
          <label for="description">Descripci&oacute;n</label>
          <textarea
              class="form-control"
              id="description"
              required
              v-model="tutorial.description"
              name="description"
          />
        </div>
        <label class="block-input">¿Como quiere mantener el tutorial?</label>
        <div class="form-group form-check-inline block">
          <div class="form-check">
            <input
                class="form-check-input"
                type="radio"
                name="published_status"
                v-model="tutorial.published_status"
                value="false">
            <label class="form-check-label">
              Oculto
            </label>
          </div>
          <div class="form-check">
            <input
                class="form-check-input"
                type="radio"
                name="published_status"
                v-model="tutorial.published_status"
                value="true">
            <label class="form-check-label">
              Publico
            </label>
          </div>
        </div>
        <ActionButton @click="saveTutorial" action="success" class="block-input">Agregar</ActionButton>
      </div>
    </div>
    <div v-else>
      <div class="card text-center">
        <div class="card-body">
          <h4>Tutorial agregado correctamente!</h4>
          <ActionButton @click="newTutorial" action="success">Agregar otro tutorial</ActionButton>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import TutorialDataService from "../services/TutorialDataService";
import ActionButton from "./slot/ActionButton";

export default {
  name: "add-tutorial",
  components: {ActionButton},

  data() {
    return {
      tutorial: {
        id: null,
        title: "",
        description: "",
        video_url: "",
        published_status: false,
      },
      errors: [],
      submitted: false
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
    },

    newTutorial() {
      this.submitted = false;
      this.tutorial = {};
    }
  }
};
</script>
<style>
.submit-form {
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