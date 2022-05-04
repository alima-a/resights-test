<template lang="pug">
  v-data-table(
    :headers="headers"
    :items="items"
    item-key="email"
    :page.sync="page"
    :items-per-page.sync="itemsPerPage"
    :loading="loading"
    :server-items-length="serverItemsLength"
    :footer-props="{'items-per-page-options': [10, 20, 50, 100]}"
    dense
  ).elevation-1.mt-10
    template(v-slot:item.name='{ item }')
      | {{ `${item.user.title}. ${item.user.first_name} ${item.user.last_name}` }}
    template(v-slot:item.email='{ item }' v-html)
      | <a :href="`mailto:${item.email}`">{{item.email}}</a>
    template(v-slot:item.color='{ item }')
      div(class="item-color" :style="`font-weight: bold; color: ${item.color};`")
        | {{item.color}}
    template(v-slot:top)
      v-container
      v-row.ma-0
        v-col(cols
        v-for="item in filterList"
          :key="item"
        )
          p.ma-0
            | {{ item.toUpperCase() }}
          v-select(
            v-model="filters[item]"
            :items="dropdowns[item]"
            item-text="name"
            item-value="value"
            :label="item"
            v-on:change="updateTableData()"
            style="flex:1"
            return-object
            single-line)
      v-row.ma-0
        v-col(class="flex-md-grow-1 d-flex")
          <v-icon>mdi-magnify</v-icon>
          v-text-field(
            v-model="search"
            class="ma-2"
            v-on:change="updateTableData"
            v-on:keyup="updateTableData"
            label="Search in all fields")
        v-col(
          style="justify-content: end"
        ).d-flex
          v-btn(
            v-on:click="clearFilters"
          ).ma-0
            | Clear Filters
    template(v-slot:no-data)
      div(class="ma-4")
        | No result
</template>

<script>
import debounce from 'lodash.debounce';

export default {
  props: ['headers', 'items', 'dropdowns', 'filterList', 'loading', 'serverItemsLength'],
  data() {
    return {
      filters:{
        gender: '',
        country: '',
        currency: '',
        color: '',
        year: '',
      },
      search: '',
      page: 1,
      itemsPerPage: 10,
    }
  },
  watch: {
    page: debounce(function (){
      this.updateTableData();
    }, 1000),
    itemsPerPage: debounce(function (){
      this.updateTableData();
    }, 1000),
  },
  methods: {
    updateTableData() {
      const options = {
        filters: this.filters,
        search: this.search,
        page: this.page,
        itemsPerPage: this.itemsPerPage,
      }
      this.$emit('refetch', options);
    },
    onSearch:debounce(function (){
      this.updateTableData();
    }, 1000),
    clearFilters(){
      this.filterList.forEach(filter => this.filters[filter] = '');
      this.search = '';
      this.updateTableData();
    },
  }
}
</script>

<style lang="sass" scoped>
  .v-data-table
    max-width: 100%
  .v-btn
    margin-left: auto
</style>
<style lang="sass">
.v-data-table tr:nth-child(even)
  background-color: #f2f2f2
</style>
