<template>
  <table class="table table-striped table-hover fx-grid">
    <thead class="thead-light">
      <tr scope="row">
        <th scope="col" class="fx-grid-col__header fx-grid-col__header-count">#</th>
        <th scope="col" class="fx-grid-col__header" v-for="col in colList" :key="col.id">
          <div>
            <div class="fx-grid-col__header-title">
              <span v-if="col.sort" class="fx-grid-col__header-sort" @click="sortColumn(col.id)">
                <span class="fx-grid-col__header-sort-asc" :class="{grey: col.sortingOrder === 'asc'}">&#8639;</span>
                <span class="fx-grid-col__header-sort-des" :class="{grey: col.sortingOrder === 'des'}">&#8642;</span>
              </span>
              <span v-else>‎‎‏‏‎</span>
              {{col.title}} 
            </div>
            <div v-if="col.filter" class="input-group input-group-sm mb-3 fx-grid-col__header-filter">
              <input type="text" class="form-control" aria-label="Small" placeholder="Search" @keyup="filterData($event, col.id)">
            </div>
          </div>
        </th>
      </tr>
    </thead>
    <tbody class="fx-grid-body">
      <tr scope="row" class="fx-grid-row" v-for="(row, i) in getPage">
        <td scope="col" class="fx-grid-cell__count">{{((index - 1) * selectedPageSize) + i + 1}}</td>
        <td scope="col" class="fx-grid-cell" v-for="col in colList" :key="col.id">{{row[col.id] ? row[col.id] : "--"}}</td>
      </tr>
    </tbody>
    <tfoot>
      <tr scope="row" v-if="pagination && numberOfPages > 1">
        <td scope="col" class="fx-grid-footer" align="right" :colspan="colList.length + 1">
          <button class="btn btn-outline-primary btn-sm fx-grid-footer__button-first-page" :disabled="index === 1" @click="changePage(0)">&lt;&lt;</button>
          <button class="btn btn-outline-primary btn-sm fx-grid-footer__button-previous-page" :disabled="index === 1" @click="changePage(-1)">&lt;</button>

          <span class="fx-grid-footer__page-indicator" > {{index}}/{{numberOfPages}} </span>

          <button class="btn btn-outline-primary btn-sm fx-grid-footer__button-next-page" :disabled="index === numberOfPages" @click="changePage(1)">&gt;</button>
          <button class="btn btn-outline-primary btn-sm fx-grid-footer__button-last-page" :disabled="index === numberOfPages" @click="changePage(numberOfPages)">&gt;&gt;</button>
        </td>
      </tr>
    </tfoot>
  </table>
</template>

<script>
import style from "./../assets/css/bootstrap.min.css"

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
    rowList: [],
    colList: [],
    filters: [],
    filteredRows: [],
  }),

  watch: {},

  computed: {
    /**
     * Get the rows for the current page
     */
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
    
    // Make a deep copy of rows
    this.rowList = JSON.parse(JSON.stringify(this.rows));
    this.filteredRows = JSON.parse(JSON.stringify(this.rows));  

    this.createSortingOrder();
    this.updateNumberOfPages();
  },
  
  methods: {
    /**
     * Called the first time when the component is initialized. Sets up the sorting order for every column
     */
    createSortingOrder() {
      const that = this;
      this.cols.forEach(col => {
        col["sortingOrder"] = "none";
        that.colList.push(col);
      });
    },
    
    /**
     * Called everytime we need to recalculate the number of pages.
     */
    updateNumberOfPages() {
      if (this.filteredRows.length % this.selectedPageSize !== 0) {
        this.numberOfPages = parseInt(this.filteredRows.length / this.selectedPageSize + 1, 10);
      } else {
        this.numberOfPages = this.filteredRows.length / this.selectedPageSize;
      }
    },

    /**
     * Called when we change the page (by clicking one of the four buttons)
     * @param pageOffset: -1/0/1/numberOfPages 
     * 0             -> go to the first page
     * -1            -> go a page back if available
     * 1             -> go a page forword if available
     * numberOfPages -> go to the last page
     */
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


    /**
     * Called ok keyup in the filter inputs
     * @param $event: if $event.which === 13(ENTER) we apply the filters
     * @param colId: the id of the column we want to filter by
     */
    filterData($event, colId) {
      if ($event.which === 13) {
        const that = this;
        const text = $event.target.value;
        const filter = this.filters.find(el => el.colId === colId);
        
        if (filter) {
          filter["text"] = text;
        } else {
          this.filters.push({colId, text});
        }
        this.filteredRows = JSON.parse(JSON.stringify(this.rowList));

        this.filters.forEach(el => {
          that.filteredRows = that.filteredRows.filter(row => {
            return row[el.colId] && (el.text === "" || row[el.colId].includes(el.text));
          });
        });

        // Reset index to avoid getting stuck on another page
        this.index = 1;
        this.updateNumberOfPages();
        // Reset all the sort buttons
        this.resetOtherHeaders();
      }  
    },

    /**
     * Called when clicking the arrows
     * @param key: the order we sort by none(default)/asc/dsc
     */
    sortColumn(key) {
      const col = this.colList.find(el => el.id === key);
      const oreder = col.sortingOrder;
      if (oreder) {
        col.sortingOrder = oreder === "none" || oreder === "des" ? "asc" : "des";
        this.sortList(key);
        this.resetOtherHeaders(col.id);
      }
    },

    /**
     * Called to sort filteredRows
     * @param key: the order we sort by none(default)/asc/dsc
     */
    sortList(key) {
      const col = this.colList.find(el => el.id === key);
      const order = col.sortingOrder === "asc" ? 1 : -1;

      this.filteredRows.sort((a, b) => {
        const e1 = a[key];
        const e2 = b[key];
        if (e1 === e2) {
          return 0;
        }
        return e1 > e2 ? 1 * order : -1 * order;
      });
    },

    /**
     * Resets all the column we are not sorting by.
     * @param id: the id of the column we need to keep
     */
    resetOtherHeaders(id) {
      this.colList.forEach(col => {
        if (col.id !== id) {
          col.sortingOrder = "none";
        }
      });
    },
  }
};
</script>

<style scoped lang="scss">
  .fx-grid {

    & .fx-grid-col__header {
      -webkit-touch-callout: none;
      -webkit-user-select: none;
      -khtml-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;
      &.fx-grid-col__header-count {
        text-align: center;
        width: auto;
      }

      & .fx-grid-col__header-sort {
        cursor: pointer;
        & .fx-grid-col__header-sort-asc,
        & .fx-grid-col__header-sort-des {
          &.grey {
            color: grey;
          }
        }
      }

      & .fx-grid-col__header-filter {
        margin-bottom: 0px !important;
        width: 150px;
      }
    }

    & .fx-grid-body {
      & .fx-grid-cell__count {
        text-align: center;
      }
    }

    & .fx-grid-footer {
        padding-right: 3rem;
        background-color: #e9ecef;
    }
  }
</style>
