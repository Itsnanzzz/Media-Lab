<template>
  <footer class="mt-10">
    <div class="max-w-6xl mx-auto px-4 py-6 text-sm text-slate-300 flex flex-col sm:flex-row items-center justify-between gap-3 border-t border-white/10">
      <p class="text-center sm:text-left">© {{ new Date().getFullYear() }} Image Converter. Crafted with Tailwind.</p>
      <div class="flex items-center gap-3">
        <span class="h-2 w-2 rounded-full bg-emerald-400 animate-pulse"></span>
        <span>Status: Online</span>
      </div>
    </div>
  </footer>
</template>

<script>
export default {
  name: 'AppFooter'
};
</script>

