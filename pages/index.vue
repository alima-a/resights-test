<template lang="pug">
  v-container
    v-row
      v-col(cols)
        DataTable(
          :headers="headers"
          :items="items"
          :dropdowns="dropdowns"
          :filter-list="filterList"
          :currentPage="pagination.currentPage",
          :pageSize="pagination.size"
          :server-items-length="pagination.total"
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
      pagination: {
        page: 1,
        itemsPerPage: 10,
        pageCount: 0,
      },
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
    await this.fetchData({ filters: null, search: ''});
  },
  methods: {
    async fetchData({ filters = null , search = '' }) {
      this.loading = true

      await this.delay(DELAY_TIME)
      let tableData = sales.results;

      this.pagination.pageCount = tableData.length;

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
          matchBySearch = JSON.stringify(item).toLowerCase().includes(search.toLowerCase())
        }

        return matchByFilters && matchBySearch
      })

      this.items = tableData;
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
