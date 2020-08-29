<template>
  <div id="app">
  
    <div>
    <b-jumbotron id="jumbotron" bg-variant="info" text-variant="white" border-variant="dark">
      <template v-slot:header>The Shoppies</template>

      <template v-slot:lead>
        Search and nominate your favourite movies here!
      </template>

      <hr class="my-4">
      <div id="listSearchOuter">
        <h2> Movie Titles</h2>
        <input class="form-control" id="listSearchInner" type="text" placeholder="Search.." @input= "isTyping = true" v-model= "searchQuery">
      </div>
      <div class="card-deck" :hidden="searchResult.length === 0">
        <div id= "searchResults" class="card">
          <img class="card-img-top" src="https://umanitoba.ca/outreach/cm/vol9/no9/cinemaverite.gif" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">Results for "{{searchQuery}}"</h5>
            <p class="card-text">            
              <b-list-group v-for="item in searchResult" :key="item">
                <b-list-group-item> {{ item.Title }} ({{item.Year}})
                  <b-button class="float-right" 
                  variant="primary" 
                  id="nomButton" 
                  @click="addNomination(item)"
                  :disabled="nominated.includes(item)">Nominate</b-button>
                </b-list-group-item>
              </b-list-group>
            </p>
          </div>
        </div>
        <div id="nominations" class="card">
          <img class="card-img-top" src="https://pyxis.nymag.com/v1/imgs/736/955/5e25633ce19528813719c5080fee29a360-05-golden-globes-tv.rsquare.w330.jpg" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">Nominations</h5>
            <p class="card-text" :hidden="nominated.length > 0">You have not nominated any movies.</p>
            <p class="card-text" :hidden="nominated.length === 0">
              <b-list-group v-for="item in nominated" :key="item">
                <b-list-group-item>{{ item.Title }} ({{item.Year}})
                  <b-button class="float-right" 
                    variant="primary" 
                    id="remButton" 
                    @click="removeNomination(item, nominated.indexOf(item))">Remove
                    </b-button>
                </b-list-group-item>
              </b-list-group>
            </p>
            </div>
          </div>
        </div>
        <div id="banner">
          <b-alert show variant="warning" v-if="maxNomination() === true">Maximum number of nominations reached</b-alert>
        </div>
    </b-jumbotron>
  </div>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

export default {
  name: 'App',
  components: {
  },
  data () {
    return {
      searchQuery: "",
      isTyping: false,
      isLoading: false,
      showBanner: false,
      searchResult: [],
      nominated: [],
    }
  },
  watch: {
    searchQuery: _.debounce( function() {
      this.isTyping = false;
    }, 1000),
    isTyping: function(value) {
      if (!value) {
        this.search(this.searchQuery);
      }
    }
  },
  methods: {
    search: function(searchQuery) {
      this.isLoading = true;
      axios
      .get('https://www.omdbapi.com/?apikey=324544c2&s=' + searchQuery)
      .then(response => {
        this.isLoading = false;
        console.log(response);
        this.searchResult = response.data.Search;
        this.nominatedDisabled = true;
      })
    },

    addNomination: function (item) {
      if (!this.nominated.includes(item)){
        this.nominated.push(item);
      }
    },

    removeNomination: function(item, index) {
      this.nominated.splice(index, 1);
    },
    
    maxNomination: function () {
      if (this.nominated.length === 5) {
        this.isTyping = false;
        this.isLoading = false;
        return true;
      } else if (this.nominated.length > 5) {
        this.isTyping = false;
        this.isLoading = false;
        this.searchQuery = "";
        this.searchResult = [];
        this.nominated = [];
        return true;
      } else {
        return false;
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background-color: #cccccc;
  margin-left: 100px;
  margin-right: 100px;
}


#listSearchOuter {
  border-radius: 20px;
  margin-left: auto;
  margin-right: auto;
  padding-left: 20px;
  padding-bottom: 30px;
  width: 90%;
  text-align:left;
}

h2 {
  padding-top: 10px;
}

#listSearchInner {
  background-color: white;
  width: 98%;
  font-size: 20px;
  height: 40px;
}

.list-group-item {
  margin-top: 5px;
  margin-bottom: 5px;
}

#searchResults {
  background-color: white;
  margin-left: 7%;
  text-align: left;
  color: black;
  width: 40%;
}

#nominations {
  background-color: white;
  color: black;
  margin-right: 7%;
  text-align: left;
  width: 40%;
}

#nomButton {
  border-radius: 5px;
  background-color:green;
  color:white;
  font-weight: bold;
}

#remButton {
  border-radius: 5px;
  background-color:lightcoral;
  color:white;
  font-weight: bold;
}

button:disabled {
  align-self: left;
  border-radius: 5px;
  background-color:green;
  color:white;
  opacity:30%;
  font-weight: bold;
}

#banner {
  padding-top: 5px;
  width: 88%;
  text-align: center;
  margin-top: 20px;
  margin-left: auto;
  margin-right: auto;
}

.card-img-top {
  width: 100%;
  height: 40vh;
  object-fit: cover;
}
</style>