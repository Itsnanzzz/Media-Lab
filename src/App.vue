<template>
  <div class="min-h-screen bg-slate-950 text-slate-100">
    <AppNavbar />
    <main>
      <Converter />
    </main>
    <AppFooter />
  </div>
</template>

<script>
import Converter from './components/Converter.vue';
import AppNavbar from './components/AppNavbar.vue';
import AppFooter from './components/AppFooter.vue';

export default {
  name: 'App',
  components: { Converter, AppNavbar, AppFooter }
};
</script>