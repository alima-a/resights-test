<template lang="pug">
  v-container
    v-row
      v-col(cols)
        DataTable(
          :headers="headers"
          :items="items"
          :dropdowns="dropdowns"
          :filterList="filterList"
          @refetch="fetchData"
        )
        v-progress-circular(
        v-if="false"
          width="2"
          color="rs__primary"
          indeterminate
        ).mx-auto
</template>

<script>
import DataTable from '~/components/DataTable.vue'
import sales from '~/api/sales.js'

export default {
  components: {
    DataTable
  },
  data() {
    return {
      sales,
      items: [],
      pageSize: 10,
      currentPage: 1,
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
      await this.delay(1000)

      let tableData = sales.results.slice(1, 100);

      if (filters) {
        this.filterList.forEach(item => {
          if(!filters[item]) return;
          tableData = tableData.filter(row =>
            row[item].toString().toLowerCase() === filters[item].value.toString().toLowerCase()
          )
        })
      }

      if(search) {
        tableData = tableData.filter(item => JSON.stringify(item).toLowerCase().includes(search.toLowerCase())
        )
      }

      this.items = tableData;
      this.fillDropdowns();
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
