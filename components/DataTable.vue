<template lang="pug">
  v-data-table(
    :headers="headers"
    :items="items"
    item-key="email"
    dense
  ).elevation-1.mt-10
    template(v-slot:item.name='{ item }')
      | {{ item.user.title + '. ' + item.user.first_name + ' ' + item.user.last_name }}
    template(v-slot:item.email='{ item }' v-html)
      | <a :href="`mailto:${item.email}`">{{item.email}}</a>
    template(v-slot:item.color='{ item }')
      div(class="item-color" :style="`font-weight: bold; color: ${item.color};`")
        | {{item.color}}
</template>

<script>
export default {
  props: ['headers', 'items'],
  data() {
    return {

    }
  }
}
</script>

<style lang="sass" scoped>
  .v-data-table
    max-width: 100%
</style>
