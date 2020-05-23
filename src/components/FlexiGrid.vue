<template>
  <div>
    <table class="fx-grid">
      <thead>
        <tr>
          <th v-for="col in colList" :key="col.id">
            <p>{{col.title}}</p>
            <p>
              <input v-if="col.filter" type="text" placeholder="Search" @keyup="filterData($event, col.id)"/>
            </p>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in getPage">
          <td v-for="col in colList" :key="col.id">{{row[col.id] ? row[col.id] : "--"}}</td>
        </tr>
      </tbody>
      <tfoot v-if="pagination && numberOfPages > 1">
        <button :disabled="index === 1" @click="changePage(0)">&lt;&lt;</button>
        <button :disabled="index === 1" @click="changePage(-1)">&lt;</button>

        <button :disabled="index === numberOfPages" @click="changePage(1)">&gt;</button>
        <button :disabled="index === numberOfPages" @click="changePage(numberOfPages)">&gt;&gt;</button>
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
    selectedPageSize: 10,
    filteredRows: [],
    rowList: [],
    colList: [],
    filters: []
  }),

  watch: {},

  computed: {
    // get the items from the current page
    getPage() {
      if (this.pagination) {
        const start = (this.index - 1) * this.selectedPageSize;
        const end =
          this.index * this.selectedPageSize > this.filteredRows.length
            ? this.filteredRows.length
            : this.index * this.selectedPageSize;
        return this.filteredRows.slice(start, end);
      }
      return this.filteredRows;
    }
  },

  created() {
    this.selectedPageSize = this.pageSize;
    
    // no initial filter
    this.rowList = this.rows;
    this.filteredRows = this.rows;  
    this.colList = this.cols;

    this.updateNumberOfPages();
  },
  
  methods: {
    changePage(pageOffset) {
      switch (pageOffset) {
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
    },

    filterData($event, colId) {
      // filter on enter
      if ($event.which === 13) {
        const that = this;
        const text = $event.target.value;
        const filter = this.filters.find(el => el.colId === colId);
        
        if (filter) {
          filter["text"] = text;
        } else {
          this.filters.push({colId, text});
        }
        this.filteredRows = this.rowList;

        this.filters.forEach(el => {
          that.filteredRows = that.filteredRows.filter(row => {
            return row[el.colId] && (el.text === "" || row[el.colId].includes(el.text));
          });
        });
        this.updateNumberOfPages();
      }  
    },

    updateNumberOfPages() {
      // determine the number of required pages
      if (this.filteredRows.length % this.selectedPageSize !== 0) {
        this.numberOfPages = parseInt(this.filteredRows.length / this.selectedPageSize + 1, 10);
      } else {
        this.numberOfPages = this.filteredRows.length / this.selectedPageSize;
      }
    }
  }
};
</script>

<style scoped lang="scss">
</style>
