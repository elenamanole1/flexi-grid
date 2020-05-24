<template>
  <div id="app" class="container">
    <div class="row">
      <h1 class="title">Flexible Grid</h1>
    </div>
    <div class="row">
      <div class="description">
        <p>Description</p>
        <p>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur quam felis, iaculis pulvinar risus id, ullamcorper sodales ante. Quisque pulvinar massa libero, nec auctor quam porta quis. Morbi dignissim velit non elit cursus, non porttitor nibh pretium. Quisque eu laoreet ante, in placerat nisi. Proin nibh orci, vulputate in quam eget, volutpat pulvinar orci.
        </p>
      </div>
    </div>
    <div class="row">
      <FlexiGrid
        :cols="cols" 
        :rows="rows" 
        :pagination="pagination" 
        :page-size="pageSize"
      />
    </div>

    <div class="row">
      <div class="properties">
        <p>Properties</p>
        <p>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur quam felis, iaculis pulvinar risus id, ullamcorper sodales ante. Quisque pulvinar massa libero, nec auctor quam porta quis. Morbi dignissim velit non elit cursus, non porttitor nibh pretium. Quisque eu laoreet ante, in placerat nisi. Proin nibh orci, vulputate in quam eget, volutpat pulvinar orci.
        </p>
      </div>
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
    pageSize: 10
  }),
  created() {
    this.rows = this.loadMockData();
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