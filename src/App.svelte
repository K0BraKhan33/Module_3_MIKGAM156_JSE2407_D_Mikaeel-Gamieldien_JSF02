<!--App.svlete-->
<script>
  import Router from 'svelte-spa-router';
  import ProductList from './Components/ProductList.svelte';
  import ProductDetail from './Components/ProductDetail.svelte';

  const routes = {
    '/': ProductList,
    '/about': ProductDetail,
  };
</script>

<Router {routes} />
