<template>
  <div>
    <table class="fx-grid">
      <thead>
        <tr>
          <th v-for="col in cols" :key="col.id">
            {{col.title}}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows">
          <td v-for="col in cols" :key="col.id">{{row[col.id] ? row[col.id] : "--"}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: {
    cols: { type: Array, required: true },
    rows: { type: Array, required: true },
    pagination: { type: Boolean, default: false },
    pageSize: { type: Number, default: 10 }
  },
  data: () => ({
    pageNumber: 0,
  }),
  computed: {},
  created() {},
  methods: {}
};
</script>

<style scoped lang="scss">
</style>
