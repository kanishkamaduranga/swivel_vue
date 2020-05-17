<template>
  <div id="app">
    <b-container>
      <Header />
      <br>
      <b-row>
        <b-col>
          <Search
            :showMainType="main_type"
            :api_url="api_url"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Search from './components/Search'

export default {
  name: 'App',
  components: {
    Header,
    Search,
  },
  data(){
    return {
      main_type: {},
      api_url: 'http://127.0.0.1:8000'
    }
  },
  mounted() {
    fetch(this.api_url+'/api/v1/main_types',{
      method: 'get'
    })
    .then((response) => {
      return response.json()
    })
    .then((jsonData) => {
      this.main_type = jsonData.data
    })
  }
}

console.log(process.env.API_URL)
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
