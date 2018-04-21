# vue-awesome-datatable (under construction)
Vue 2 DataTable

Vue Awesome DataTable aims to provide a lightweight, dependency-free and easy-to-use DataTable with the most important features available, which are:

* Filtering (Whole columns or specific columns);
* Ordering (asc/desc);
* Pagination

You can start using it by just importing, registering and passing the available props:

Import

```javascript
import VueAwesomeTable from './components/VueAwesomeTable'
```
Register

```javascript
{
  components: {
    VueAwesomeTable
  }
}
```

Add the component at the HTML passing the props

```html
<vue-awesome-table 
  :columns='columns'
  :data='data'
  :filter='filter'
  :filtered-keys='filteredKeys'/>
```

The above object props should look like this

```javascript
{
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
  filteredKeys: []  // Keys which will be used to filter the table
}
```
