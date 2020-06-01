<template>
  <div id="app" class="container">
    <div class="row">
      <h1 class="title">Flexible Grid</h1>
    </div>

    <div class="row">
      <div class="properties">
        <p>Paginated table</p>
      </div>
    </div>

    <div class="row">
      <FlexiGrid
        :cols="table1.cols" 
        :rows="table1.rows" 
        :pagination="table1.pagination" 
        :page-size="table1.pageSize"
      />
    </div>

    <div class="row">
      <div class="properties">
        <p>Infinite scrolling table</p>
      </div>
    </div>

    <div class="row">
      <FlexiGrid
        :cols="table2.cols" 
        :rows="table2.rows" 
        :pagination="table2.pagination" 
        :page-size="table2.pageSize"
        :infinite-scrolling="table2.infiniteScrolling"
      />
    </div>
  </div>
</template>
<script>
import FlexiGrid from "./components/FlexiGrid.vue";
import mockData from "./assets/MOCK_DATA.json";
import style from "./assets/css/bootstrap.min.css"

export default {
  components: {
    FlexiGrid
  },
  data: () => ({
    table1: {
      cols: [
        {
          id: "first_name",
          title: "First Name",
          sort: true,
          filter: true
        },
        {
          id: "email",
          title: "Email",
          sort: false,
          filter: true
        },
        {
          id: "last_name",
          title: "Last Name",
          sort: true,
          filter: true
        },
        
        {
          id: "adress",
          title: "Adress",
          sort: true,
          filter: true
        },
        {
          id: "phone_number",
          title: "Phone Number",
          filter: false
        }
      ],
      rows: [],
      pagination: true,
      pageSize: 10,
    },
    table2: {
      cols: [
        {
          id: "first_name",
          title: "First Name",
        },
        {
          id: "email",
          title: "Email",
        },
        {
          id: "last_name",
          title: "Last Name",
        },
        
        {
          id: "adress",
          title: "Adress",
        },
        {
          id: "phone_number",
          title: "Phone Number",
        }
      ],
      rows: [],
      pagination: true,
      pageSize: 10,
      infiniteScrolling: true
    }
  }),
  created() {
    this.table1.rows = JSON.parse(JSON.stringify(this.loadMockData()));
    this.table2.rows = JSON.parse(JSON.stringify(this.loadMockData(101)));
  },
  methods: {
    loadMockData(nrOfRows) {
      if (nrOfRows && nrOfRows < mockData.length) {
        return mockData.splice(0, nrOfRows);
      }
      return mockData;
    }
  }
};
</script>

<style scoped lang="scss">
  .title {
    margin-top: 1rem;
    margin-bottom: 0.5rem;
    font-size: 3rem;
    font-weight: 400;
  }

  .description,
  .properties {
    margin-bottom: 1rem;
    font-size: 1.5rem;
  }

  .description {
    max-width: 80%;
  }
</style>