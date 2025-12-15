<template>
  <header class="sticky top-0 z-20">
    <div class="backdrop-blur-xl bg-white/5 border-b border-white/10">
      <div class="max-w-6xl mx-auto px-4 py-4 flex items-center justify-between">
        <div class="flex items-center gap-3">
          <div class="h-10 w-10 rounded-xl bg-gradient-to-br from-blue-500 to-cyan-400 flex items-center justify-center text-white shadow-lg shadow-cyan-500/30">
            <span class="text-lg font-semibold">ML</span>
          </div>
          <div>
            <p class="text-sm uppercase tracking-[0.3em] text-slate-300">Media Lab</p>
          </div>
        </div>

        <div class="hidden sm:flex items-center gap-3 text-xs">
          <span class="px-3 py-1 rounded-full bg-white/10 border border-white/10 text-slate-200">
            Glassmorphism UI
          </span>
          <span class="px-3 py-1 rounded-full bg-white/10 border border-white/10 text-slate-200">
            Tailwind Powered
          </span>
        </div>

        <a
          href="https://github.com/"
          target="_blank"
          rel="noreferrer"
          class="inline-flex items-center gap-2 px-3 py-2 rounded-lg bg-gradient-to-r from-blue-500 to-cyan-400 text-sm font-semibold text-white shadow-md shadow-cyan-500/20 hover:shadow-cyan-500/40 transition"
        >
          <span>Repository</span>
        </a>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: 'AppNavbar'
};
</script>

