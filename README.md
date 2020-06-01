# Flexi Grid - Flexible grid for handling large data
FlexiGrid is a vue.js grid component that offers client side pagination, sorting and filtering.


## Demo Example 
DemoView.vue offers an example for how to integrate this component.
To run the demo, use the following commands:

```
npm install
npm run serve
```

When startup completes, you can access it via http://localhost:8080/.

## Component Usage

In order to use this component just import it in your project:

```js
import FlexiGrid from "./components/FlexiGrid.vue";
```

<!--
```js
<template>
``` -->

FlexiGrid exposes the following properties:

* cols: `Array` - a list of columns for the grid 
* rows: `Array` - a list of rows for the grid
* pagination: `Boolean` - default `false`, set it to `true` to allow pagination
* page-size: `Number`- default `10`, specify how many rows should be loaded for a page
* infiniteScrolling: `Boolean`- default `false`, set it to `true` to allow infinite scrolling


### `Cols`
An array of columns should have the following structure:
```js
cols: [
  {
    id: "first_name",
    title: "First Name",
    sort: true,
    filter: true
  }
],
```
* id: `any` - is mandatory and will determin the id of a given column
* title: `any` - optiona, will determin the column header
* sort: `Boolean` - default `false`, if set to `true` will allow sorting on that column
* filter: `Boolean` - default `false`, if set to `true` will allow filtering on that column

The order of columns in grid, is determined by the order of columns in the array.

### `Rows`
The rows arrays should have the following structure:
```js
// Structure: <column_id, value>
rows: [
  {
    "first_name":"Noelyn",
    "last_name":"Wharby",
    "adress":"2 Rockefeller Court",
    "phone_number":"345-517-6590"
  }
]
```
Keep in mind that providing values for a given column is option, that column will be populated with  `--` if nothing is specified.

## Component functionalities

### Pagination

If the pagination is enabled, only a number of entries are shown at a time in the grid, and a navigation panel is displayed, showing the current page and the total number of pages. 
You can also jump to the last or to the firstpage.

![Pagination](src\assets\images\pagination.png)

### Filter and Sorting

The table cols also have some sorting and filter properties. If sorting is enabled, the up and down arrows appear next to the header. If filter is enabled, a text input is diplayed below the header, allowing the user to type in a query string.

In this example, First Name, Last Name and Adress can be ordered ascending or descending. First Name, E-mail, Last Name and Adress can be filtered. 

![Headers with filer and sort properties](src\assets\images\sortfilter.png)

The result of the filters is also paginated and multiple filters can be applied at once. For sorting, only one criteria can be chosen at a time. 

The following gif illustratesa sorting and filtering example:

![Filter and sort example](src\assets\images\filter-sort.gif)


### Infinite scrolling

If infinite scrolling is enabled, the data can be viewed in a continuous single page mode. It is loaded in chunks (defined by `pageSize`), when scrolling to the end of the current chunk, new data will be loaded and displayed continuously.

![Infinite Scrolling](src\assets\images\infinite-scroll.gif)

## Here you can see a full example for how to implement FlexiGrid
```html
<template>
  <FlexiGrid
    :cols="cols" 
    :rows="rows" 
    :pagination="pagination" 
    :page-size="pageSize"
    :infinite-scrolling="infiniteScrolling"
  />
</template>
<script>
data: () => ({
  cols: [
    {
      id: "first_name",
      title: "First Name",
      sort: true,
      filter: true
    }
    // ...
  ],
  rows: [
    {
      "first_name":"Noelyn",
      "last_name":"Wharby",
      "adress":"2 Rockefeller Court",
      "phone_number":"345-517-6590"
    }
  // ...
  ],
  pagination: true,
  pageSize: 10,
  infiniteScrolling: false
})
</script>
```