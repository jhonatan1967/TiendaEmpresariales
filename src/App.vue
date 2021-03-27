<template>
  <v-app>
    <v-main>
      <h2>Tienda Jhonatan</h2>
      <ProductsDataTable/>
    </v-main>
  </v-app>
</template>

<script>
import ProductsDataTable from './components/ProductsDataTable';

export default {
  name: 'App',

  components: {
    ProductsDataTable,
  },

  data: () => ({
    //
  }),
};
</script>
