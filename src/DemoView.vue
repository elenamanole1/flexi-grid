<template>
  <div id="app">
    <h1 class="title">Flexible Grid</h1>
    <FlexiGrid class="grid" :cols="cols" :rows="rows" :pagination="pagination" :page-size="pageSize"/>
  </div>
</template>
<script>
import FlexiGrid from "./components/FlexiGrid.vue";
import mockData from "./assets/MOCK_DATA.json";

export default {
  components: {
    FlexiGrid
  },
  data: () => ({
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
        sort: true,
        filter: false
      }
    ],
    rows: [],
    pagination: true,
    pageSize: 21
  }),
  created() {
    this.rows = this.loadMockData(200);
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
</style>