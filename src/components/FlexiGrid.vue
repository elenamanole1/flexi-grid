<template>
  <div>
    <table class="fx-grid">
      <thead>
        <tr>
          <th class="fx-grid-col__header fx-grid-col__header-count">#</th>
          <th class="fx-grid-col__header" v-for="col in colList" :key="col.id">
            <p class="fx-grid-col__header-title">
              {{col.title}} 
              <span class="fx-grid-col__header-sort" @click="sortColumn(col.id)">
                <span class="fx-grid-col__header-sort-asc" :class="{grey: col.sortingOrder === 'asc'}">&#8639;</span>
                <span class="fx-grid-col__header-sort-des" :class="{grey: col.sortingOrder === 'des'}">&#8642;</span>
              </span>
            </p>
            <p class="fx-grid-col__header-filter"><input v-if="col.filter" type="text" placeholder="Search" @keyup="filterData($event, col.id)"/></p>
          </th>
        </tr>
      </thead>
      <tbody class="fx-grid-body">
        <tr class="fx-grid-row" v-for="(row, i) in getPage">
          <td class="fx-grid-cell__count">{{((index - 1) * selectedPageSize) + i + 1}}</td>
          <td class="fx-grid-cell" v-for="col in colList" :key="col.id">{{row[col.id] ? row[col.id] : "--"}}</td>
        </tr>
        <tr v-if="pagination && numberOfPages > 1">
          <td class="fx-grid-footer" align="right" :colspan="colList.length + 1">
            <button class="fx-grid-footer__button-first-page" :disabled="index === 1" @click="changePage(0)">&lt;&lt;</button>
            <button class="fx-grid-footer__button-previous-page" :disabled="index === 1" @click="changePage(-1)">&lt;</button>

            <span class="fx-grid-footer__page-indicator" > {{index}}/{{numberOfPages}} </span>

            <button class="fx-grid-footer__button-next-page" :disabled="index === numberOfPages" @click="changePage(1)">&gt;</button>
            <button class="fx-grid-footer__button-last-page" :disabled="index === numberOfPages" @click="changePage(numberOfPages)">&gt;&gt;</button>
          </td>
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
        this.filteredRows = JSON.parse(JSON.stringify(this.rowList));

        this.filters.forEach(el => {
          that.filteredRows = that.filteredRows.filter(row => {
            return row[el.colId] && (el.text === "" || row[el.colId].includes(el.text));
          });
        });

        // Reset index
        this.index = 1;
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
            col.sortingOrder = "asc";
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
    width: 100%;
    border-spacing: 0;
    border-top: 1px solid black;
    border-left: 1px solid black;
    border-bottom: 1px solid black;

    & .fx-grid-col__header {
      -webkit-touch-callout: none;
      -webkit-user-select: none;
      -khtml-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;
      text-align: left;
      border-bottom: 1px solid black;
      border-right: 1px solid black;
      padding-right: 10px;
      padding-left: 10px;

      & .fx-grid-col__header-title {
        margin-bottom: 0;
      }

      & .fx-grid-col__header-filter {
        margin-top: 0;
      }

      &.fx-grid-col__header-count {
        min-width: auto;
        max-width: auto;
        text-align: center;
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
    }

    & .fx-grid-body {
      & .fx-grid-cell,
      & .fx-grid-cell__count {
        height: 30px;
        padding-left: 5px;
        padding-right: 5px;
        border-bottom: 1px solid black;
        border-right: 1px solid black;
      }

      & .fx-grid-cell__count {
        text-align: center;
      }
    }

    & .fx-grid-footer {
        height: 50px;
        border-right: 1px solid black;
        padding-right: 50px;
    }
  }
</style>
