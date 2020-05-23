<template>
  <div>
    <table class="fx-grid">
      <thead>
        <tr>
          <th>#</th>
          <th v-for="col in colList" :key="col.id">
            <p>{{col.title}} 
              <span class="fx-grid-sort-header" @click="sortColumn(col.id)">
                <span v-if="col.sortingOrder === 'asc' || col.sortingOrder === 'none'">&#10224;</span>
                <span v-if="col.sortingOrder === 'des' || col.sortingOrder === 'none'">&#10225;</span>
              </span>
              </p>
            <p>
              <input v-if="col.filter" type="text" placeholder="Search" @keyup="filterData($event, col.id)"/>
            </p>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, i) in getPage">
          <td>{{((index - 1) * selectedPageSize) + i + 1}}</td>
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
    this.rowList = JSON.parse(JSON.stringify(this.rows));
    this.filteredRows = JSON.parse(JSON.stringify(this.rows));  

    this.createSortingOrder();
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
        this.filteredRows = JSON.parse(JSON.stringify(this.rowList));;

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
    },

    createSortingOrder() {
      const that = this;
      this.cols.forEach(col => {
        col["sortingOrder"] = "none";
        that.colList.push(col);
      });
    },

    sortColumn(key) {
      const col = this.colList.find(el => el.id === key);
      if (col.sortingOrder) {
        switch (col.sortingOrder) {
          case "none":
            col.sortingOrder = "asc";
            break;
          case "asc":
            col.sortingOrder = "des";
            break;
          case "des":
            col.sortingOrder = "none";
            break;
          default:
            break;
        }
        this.sortList(key);
      }
    },

    sortList(key) {
      const col = this.colList.find(el => el.id === key);
      const order = col.sortingOrder === "asc" ? 1 : -1;
      if (col.sortingOrder !== "none") {
        this.filteredRows.sort((a, b) => {
          const e1 = a[key];
          const e2 = b[key];
          if (e1 === e2) {
            return 0;
          }
          return e1 > e2 ? 1 * order : -1 * order;
        });
      } else {
        this.filteredRows = JSON.parse(JSON.stringify(this.rowList));
      }
    }
  }
};
</script>

<style scoped lang="scss">
  .fx-grid {
    & .fx-grid-sort-header {
      cursor: pointer;
    }
  }
</style>
