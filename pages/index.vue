<template lang="pug">
  v-container
    v-row
      v-col(cols)
        DataTable(
          :headers="headers"
          :items="items"
          :dropdowns="dropdowns"
          :filter-list="filterList"
          :server-items-length="pageCount"
          :loading="loading"
          @refetch="fetchData"
        )
</template>

<script>
import DataTable from '~/components/DataTable.vue'
import sales from '~/api/sales.js'

const DELAY_TIME = 3000;

export default {
  components: {
    DataTable
  },
  data() {
    return {
      sales,
      items: [],
      loading: true,
      headers: [
        {text: 'ID', value: 'id', align: 'start',},
        {text: 'Name', value: 'name'},
        {text: 'Email', value: 'email'},
        {text: 'Gender', value: 'gender'},
        {text: 'IP', value: 'ip_address'},
        {text: 'Year', value: 'year'},
        {text: 'Sales', value: 'sales'},
        {text: 'Country', value: 'country'},
        {text: 'Currency', value: 'currency'},
        {text: 'Color', value: 'color'}
      ],
      pageCount: 0,
      baseOptions: {
        filters: null,
        search: '',
        page: 1,
        itemsPerPage: 10,
      },
      filterList: [
        'gender',
        'country',
        'currency',
        'year',
        'color'
      ],
      dropdowns: {}
    }
  },
  async created() {
    await this.fetchData(this.baseOptions);
  },
  methods: {
    async fetchData(options) {
      const {
        filters,
        search,
        page,
        itemsPerPage,
      } = options;
      this.loading = true

      await this.delay(DELAY_TIME)
      let tableData = sales.results;

      tableData = tableData.filter(item => {
        let matchByFilters = true
        let matchBySearch = true

        if (filters) {
          this.filterList.forEach(filter => {
            if(filters[filter]?.value && filters[filter].value !== item[filter]) {
              matchByFilters = false
            }
          })
        }

        if(search) {
          matchBySearch = Object.values(item).join().toLowerCase().includes(search.toLowerCase())
        }

        return matchByFilters && matchBySearch
      })

      const from = page - 1
      const to = from + itemsPerPage

      this.items = tableData.slice(from, to);
      this.pageCount = tableData.length;
      this.fillDropdowns();
      this.loading = false
    },
    delay(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    },
    fillDropdowns() {
      const defaultDropdownValue = {
        name: 'Show all',
        value: ''
      }
      this.filterList.forEach(filter => {
        const uniqueValues = Array.from(new Set(this.items.map(item => item[filter]))).sort();
        const dropdownData = uniqueValues.map(item => {
          return {
            name: item,
            value: item
          }
        })

        this.dropdowns[filter] = [ defaultDropdownValue, ...dropdownData ]
      })
    },
  }
}
</script>

<style lang="sass" scoped>
.v-progress-circular
  position: absolute
  top: 50%
  left: 50%
</style>
