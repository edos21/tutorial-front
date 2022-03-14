<template>
  <div v-if="searchResult">
    <div v-if="!tutorials || tutorials.length">
    <div class="list row">
      <div class="col-md-12">
        <div class="input-group mb-5 mt-3">
          <input type="text" class="form-control" placeholder="Buscar tutorial"
                 v-model="title" v-on:keyup.enter="searchTitle"/>
          <div class="input-group-append">
            <ActionButton @click="searchTitle" action="outline-secondary">Buscar</ActionButton>
          </div>
        </div>
      </div>
        <div class="col-md-4">
          <h4 class="mb-4">Lista de tutoriales</h4>
          <ul class="list-group">
            <li class="list-group-item list-group-item-action"
                :class="{ active: index == currentIndex }"
                v-for="(tutorial, index) in tutorials"
                :key="index"
                @click="setActiveTutorial(tutorial, index)"
            >
              {{ tutorial.title }}
            </li>
          </ul>
          <ActionButton @click="removeAllTutorials" action="danger" class="btn-sm m-3">Eliminar Todos</ActionButton>
        </div>
        <div class="col-md-8">
          <div v-if="currentTutorial">
            <DetailTutorial :currentTutorial="currentTutorial" />
          </div>
          <div v-else>
            <h4 class="mb-4">Tutorial</h4>
            <div class="alert alert-primary" role="alert">
              <p>Seleccionar un tutorial para ver su contenido...</p>
            </div>
          </div>
        </div>
    </div>
    </div>
    <div v-else>
      <div class="card text-center">
        <div class="card-body">
          <h4 class="card-title">No hay tutoriales aqui</h4>
          <p class="card-text">Comencemos por <router-link to="/add">agregar</router-link> uno</p>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <div class="card text-center">
      <div class="card-body">
        <h4 class="card-title">No hay resultados de busqueda para "{{title}}"</h4>
        <p class="card-text">
          <ActionButton @click="refreshList" action="link">Mostrar todos</ActionButton>
        </p>
      </div>
    </div>
  </div>
</template>
<script>
import TutorialDataService from "../services/TutorialDataService";
import ActionButton from "./slot/ActionButton";
import DetailTutorial from "@/components/DetailTutorial";

export default {
  name: "tutorials-list",
  components:{DetailTutorial, ActionButton},
  data() {
    return {
      tutorials: [],
      currentTutorial: null,
      currentIndex: -1,
      title: "",
      searchResult : true,
    };
  },
  methods: {
    retrieveTutorials() {
      TutorialDataService.getAll()
          .then(response => {
            this.tutorials = response.data;
            console.log(response.data);
          })
          .catch(e => {
            console.log(e);
          });
    },
    refreshList() {
      this.retrieveTutorials();
      this.currentTutorial = null;
      this.currentIndex = -1;
      this.searchResult = true
      this.title = ""
    },
    setActiveTutorial(tutorial, index) {
      this.currentTutorial = tutorial;
      this.currentIndex = tutorial ? index : -1;
    },
    removeAllTutorials() {
      TutorialDataService.deleteAll()
          .then(response => {
            console.log(response.data);
            this.refreshList();
          })
          .catch(e => {
            console.log(e);
          });
    },

    searchTitle() {
      TutorialDataService.findByTitle(this.title)
          .then(response => {
            this.tutorials = response.data;
            if (response.data.length < 1){
              this.searchResult = false
            }
            this.setActiveTutorial(null);
            console.log(response.data);
          })
          .catch(e => {
            console.log(e);
          });
    }
  },
  mounted() {
    this.retrieveTutorials();
  }
};
</script>
<style>
.list {
  text-align: left;
  max-width: 1050px;
  margin: auto;
}
</style>