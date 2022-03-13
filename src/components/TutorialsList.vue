<template>
  <div v-if="!tutorials || tutorials.length">
  <div class="list row">
    <div class="col-md-12">
      <div class="input-group mb-5 mt-3">
        <input type="text" class="form-control" placeholder="Buscar tutorial"
               v-model="title"/>
        <div class="input-group-append">
          <button class="btn btn-outline-secondary" type="button"
                  @click="searchTitle"
          >
            Buscar
          </button>
        </div>
      </div>
    </div>
      <div class="col-md-4">
        <h4 class="mb-4">Lista de tutoriales</h4>
        <ul class="list-group">
          <li class="list-group-item"
              :class="{ active: index == currentIndex }"
              v-for="(tutorial, index) in tutorials"
              :key="index"
              @click="setActiveTutorial(tutorial, index)"
          >
            {{ tutorial.title }}
          </li>
        </ul>
        <button class="m-3 btn btn-sm btn-danger" @click="removeAllTutorials">
          Remove All
        </button>
      </div>
      <div class="col-md-8">
        <div v-if="currentTutorial">
          <h4>Tutorial</h4>
          <div>
            <label><strong>Title:</strong></label> {{ currentTutorial.title }}
          </div>
          <div>
            <label><strong>Description:</strong></label> {{ currentTutorial.description }}
          </div>
          <div>
            <label><strong>Status:</strong></label> {{ currentTutorial.published_status ? "Published" : "Pending" }}
          </div>
          <router-link :to="'/tutorials/' + currentTutorial.id" class="badge badge-warning">Edit</router-link>
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
</template>
<script>
import TutorialDataService from "../services/TutorialDataService";
export default {
  name: "tutorials-list",
  data() {
    return {
      tutorials: [],
      currentTutorial: null,
      currentIndex: -1,
      title: ""
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