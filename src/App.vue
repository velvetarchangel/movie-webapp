<template>
  <div id="app">
      <h1 > The Shoppies </h1>
      <div id="listSearchOuter">
      <h2> Movie Titles</h2>
      <input class="form-control" id="listSearchInner" type="text" placeholder="Search.." @input= "isTyping = true" v-model= "searchQuery">
      <br>
      </div>
    <table id="resultTables">
      <th>Results for "{{searchQuery}}"</th>
      <th></th>
      <th></th>
      <th>Nomination list</th>
        <tr>
          <td id="searchResults">
            <ul class="list-group">
              <li class="list-group-item" v-for="item in searchResult" :key="item">{{ item.Title }} ({{item.Year}})</li>
            </ul>
          </td>
          <td id="nominateButton">
            <ul>
              <li class="list-group-item" v-for="item in searchResult" :key="item">
                <button id="nomButton" @click="addNomination(item)"
                  :disabled="nominated.includes(item)"
                  >Nominate</button>
              </li>
            </ul>
          </td>
          <td id="spacer" style="width:5%; background-color: lightgrey"></td>
          <td id="nominations">
            <ul class="list-group">
              <li class="list-group-item" v-for="item in nominated" :key="item">{{ item.Title }} ({{item.Year}})</li>
            </ul>
          </td>
          <td id="removeButton">
            <ul>
              <li class="list-group-item" v-for="item in nominated" :key="item">
                <button id="remButton" @click="removeNomination(item, nominated.indexOf(item))">Remove</button>
              </li>
            </ul>
          </td>
        </tr>
    </table>
    <div id="banner" v-if="maxNomination() === true">
      Maximum number of nominations reached
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'

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
  margin-left: 15px;
  margin-right: 15px;
}


#listSearchOuter {
  border-radius: 20px;
  margin-left: auto;
  margin-right: auto;
  padding-left: 20px;
  padding-bottom: 30px;
  background-color: white;
  width: 90%;
  text-align:left;
}

h2 {
  padding-top: 10px;
}

#listSearchInner {
  background-color: white;
  width: 90%;
  height: 30px;
}

.list-group-item {
  margin-top: 5px;
  margin-bottom: 5px;
}

table{

  width: 92%;
  margin-left: auto;
  margin-right: auto;
  padding-top:30px;
  border-spacing: 0px;
}

.resultsTable td {
  padding: 0 10px 0 0;
}

#listGroup {
  background-color: white;
}

#searchResults {
  background-color: white;
  text-align: left;
  width: 45%;
}

#nominations {
  background-color: white;
  text-align: left;
  width: 45%;
}

#spacer {
  width: 5%;
}

tr {
  background-color: white;
}

ul {
  list-style-type: none;
}

th {
  text-align: center;
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
  width: 92%;
  text-align: center;
  color: black;
  margin-top: 20px;
  margin-left: auto;
  margin-right: auto;
  background-color: #66ffc2;
  border: 1px solid #003300;
  border-radius: 25px;
}
</style>