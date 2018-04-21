<template>
  <table cellspacing="0">
    <thead>
      <tr>
          <th v-for="column in visibleColumns" :key='column.name'
              :class="{ sortable: column.sortable }" @click="sortBy(column)">
            {{ column.name }}
            <span v-if='column.sortable && orderColumn !== column.name'>'s'</span>
            <span v-else-if="column.sortable">{{ order > 0 ? 'asc' : 'desc' }}</span>
          </th>
      </tr>
    </thead>
    <tbody>
      <tr v-if="!filteredData.length">
        <td style="text-align:center;" :colspan="visibleColumns.length">No registries to show</td>
      </tr>
      <tr v-else v-for="data in filteredData" :key="data.key || JSON.stringify(data)">
        <td v-for="column in visibleColumns" :key='column.name'>
            <div v-if="!filter.length">{{ data[column.name] }}</div>
            <div v-else v-html='highlight(data[column.name])'></div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  props: {
    columns: {
      type: Array,
      default: () => []
    },
    data: {
      type: Array,
      default: () => []
    },
    filter: {
      type: String,
      default: ''
    },
    filteredKeys: {
      types: Array,
      default: () => []
    },
    initialOrderColumn: {
      type: Array,
      default: null
    }
  },
  data () {
    return {
      order: -1,
      orderColumn: this.initialOrderColumn
    }
  },
  computed: {
    visibleColumns () {
      return this.columns.filter((c) => c.visible === undefined || c.visible)
    },
    filteredData () {
      let data = this.data
      let filter = this.filter.toLowerCase()
      let orderColumn = this.orderColumn
      let order = this.order

      if (filter.length) {
        let keysToFilter = this.filteredKeys.length > 0 ? this.filteredKeys : this.visibleColumns.map(vc => vc.name)
        data = data.filter(d => keysToFilter.some(k => d[k].toString().toLowerCase().indexOf(filter) > -1))
      }

      if (orderColumn) {
        data = data.sort((a, b) => {
          a = a[orderColumn]
          b = b[orderColumn]
          return (a === b ? 0 : a > b ? 1 : -1) * order
        })
      }

      return data
    }
  },
  methods: {
    sortBy (column) {
      if (!column.sortable) {
        return
      }

      if (this.orderColumn !== column.name) {
        this.order = 1
      } else {
        this.order *= -1
      }
      this.orderColumn = column.name
    },
    highlight (val) {
      if (!val) {
        return val
      }
      val = val.toString()
      let lowerVal = val.toString().toLowerCase()
      let filter = this.filter.toLowerCase()

      if (!val.length || !filter.length) {
        return val
      }

      let idx = lowerVal.indexOf(filter)

      if (idx === -1) {
        return val
      }

      let left = val.substring(0, idx)
      let mark = val.substring(idx, idx + filter.length)
      let right = val.substring(idx + filter.length, filter.length + val.length)
      return `${left}<mark>${mark}</mark>${right}`
    }
  }
}
</script>

<style scoped>
table {
  background-color: #ddd;
  width: 100%;
}

table tr th { border-top: 1px solid #333; }

table tr th.sortable { cursor: pointer; }

table th:first-child,
table td:first-child { border-left: 1px solid #333; }

table th,
table td {
  border-right: 1px solid #333;
  border-bottom: 1px solid #333;
}
</style>
