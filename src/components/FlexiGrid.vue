<template>
  <div>
    <table class="fx-grid">
      <thead>
        <tr>
          <th v-for="col in cols" :key="col.id">{{col.title}}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in getPage">
          <td v-for="col in cols" :key="col.id">{{row[col.id] ? row[col.id] : "--"}}</td>
        </tr>
      </tbody>
      <tfoot v-if="pagination">
        <button :disabled="index === 1" @click="changePage(-1)">&lt;</button>
        <button :disabled="index === numberOfPages" @click="changePage(1)">&gt;</button>
        <span>{{index}}/{{numberOfPages}}</span>
      </tfoot>
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
    index: 1,
    numberOfPages: 1,
    selectedPageSize: 10
  }),
  watch: {},
  computed: {
    getPage() {
      if (this.pagination) {
        const start = (this.index - 1) * this.selectedPageSize;
        const end =
          this.index * this.selectedPageSize > this.rows.length
            ? this.rows.length
            : this.index * this.selectedPageSize;
        return this.rows.slice(start, end);
      }
      return this.rows;
    }
  },
  created() {
    this.selectedPageSize = this.pageSize;

    if (this.rows.length % this.selectedPageSize !== 0) {
      this.numberOfPages = parseInt(this.rows.length / this.selectedPageSize + 1, 10);
    } else {
      this.numberOfPages = this.rows.length / this.selectedPageSize;
    }
  },
  methods: {
    changePageSize($event) {
      const newPageSize = $event.target.value;
      this.selectedPageSize =
        newPageSize === "MAX" ? this.rows.length : newPageSize;
    },
    changePage(where) {
      switch (where) {
        case 0:
          this.index = 1;
          break;
        case this.numberOfPages:
          this.index = this.numberOfPages;
          break;
        case 1:
          if (this.index + 1 <= this.numberOfPages) {
            this.index = this.index + 1;
          }
          break;
        case -1:
          if (this.index - 1 > 0) {
            this.index = this.index - 1;
          }
          break;
        default:
          break;
      }
    }
  }
};
</script>

<style scoped lang="scss">
</style>
