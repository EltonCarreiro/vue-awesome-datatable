<template>
  <table cellspacing="0">
    <thead>
      <tr>
          <th v-for="column in visibleColumns" :key='column.name'
              class='noselect' :class="{ sortable: column.sortable }" @click="sortBy(column)">
            {{ column.displayName !== undefined ? column.displayName: column.name }}
            <span v-if="column.sortable">
              <span v-if='orderColumn !== column.name' class="arrow-container">
                <span class="arrow up"></span>
                <span class="arrow down"></span>
              </span>
              <span v-else class="arrow" :class="order > 0 ? 'asc' : 'desc'"></span>
            </span>
          </th>
      </tr>
    </thead>
    <tbody>
      <tr v-if="!filteredData.length">
        <slot name='@row-empty' v-bind='{ allData: data, allColumns: columns, visibleColumns }'>
          <td style="text-align:center;" :colspan="visibleColumns.length">No registries to show</td>
        </slot>
      </tr>
      <tr v-else v-for="data in filteredData" :key="data.key || JSON.stringify(data)">
        <slot name="@row" v-bind='{ allData: data, data: filteredData, allColumns: columns, visibleColumns }'>
          <td v-for="column in visibleColumns" :key='column.name'>
            <slot :name='column.name' v-bind='{ allData: data, data: filteredData, currentData: filteredData[column.name], allColumns: columns, visibleColumns, currentColumn: column }'>
              <div v-if="!filter.length">{{ data[column.name] }}</div>
              <div v-else v-html='highlight(data[column.name])'></div>
            </slot>
          </td>
        </slot>
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
      type: Array,
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
  width: 100%;
  border: 1px solid #ddd;
}

th { border-bottom: 2px solid #ddd; }

th.sortable { cursor: pointer; }

th,
td {
  border-bottom: 1px solid #ddd;
  padding: 6px;
}

tbody tr:hover { background-color: #eaeaea; }

tbody tr:nth-child(odd) { background-color:#f9f9f9; }

.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #333;
}

.arrow.desc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #333;
}

.noselect {
  -webkit-touch-callout: none;
    -webkit-user-select: none;
     -khtml-user-select: none;
       -moz-user-select: none;
        -ms-user-select: none;
            user-select: none;
}

.arrow-container {
  position: relative;
}

.arrow.up {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #333;
  position: absolute;
  top: 25%;
  opacity: 0.6;
}

.arrow.down {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #333;
  position: absolute;
  top: 65%;
  opacity: 0.6;
}

</style>
