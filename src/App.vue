<template>
  <div id="app">
    <label>Search</label>
    <input type="text" v-model="filter">
    <br>
    <label>Filtered keys</label>
    <br>
    <div style="display: inline-block; margin-right: 18px;">
      <input type="checkbox" id='allkeys'
        :checked='!filteredKeys.length || filteredKeys.length == allKeysToFilter.length'
        @click="filteredKeys.splice(0, filteredKeys.length)">
      <label for="">All keys</label>
    </div>
    <div style="display: inline-block; margin-right: 18px;" v-for="key in allKeysToFilter" :key='key'>
      <input type="checkbox" :id='key' :checked='isFiltered(key)' @click="toggleFilter(key)" >
      <label :for="key">{{ key }}</label>
    </div>
    <vue-awesome-datatable :columns='columns' :data='data' :filter='filter' :filtered-keys='filteredKeys'/>
  </div>
</template>

<script>
import VueAwesomeDatatable from './components/VueAwesomeDatatable'

export default {
  name: 'App',
  data () {
    return {
      columns: [
        { name: 'product', sortable: true },
        { name: 'stock', sortable: true }
      ],
      data: [
        { product: 'Shampoo', stock: 37 },
        { product: 'Soap', stock: 12 },
        { product: 'Toilet paper', stock: 45 }
      ],
      filter: '',
      filteredKeys: []
    }
  },
  computed: {
    allKeysToFilter () {
      return this.columns
        .filter(c => c.visible === undefined || c.visible)
        .map(c => c.name)
    }
  },
  methods: {
    isFiltered (key) {
      return this.filteredKeys.length === 0 || this.filteredKeys.some(k => k === key)
    },
    toggleFilter (key) {
      if (this.isFiltered(key)) {
        if (this.filteredKeys.length === 1) {
          return
        }

        this.filteredKeys.splice(this.filteredKeys.indexOf(key), 1)
      } else {
        this.filteredKeys.push(key)
      }
    }
  },
  mounted () {
    this.allKeysToFilter.map(k => this.filteredKeys.push(k))
  },
  components: {
    VueAwesomeDatatable
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  width: 50%;
  margin: 60px auto;
}
</style>
