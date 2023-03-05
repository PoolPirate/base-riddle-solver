<script lang="ts">
  import Web3Modal from "$lib/components/Web3Modal.svelte";
  import "../app.css";
</script>

<div class="flex flex-col h-screen text-slate-200">
  <header
    class="flex flex-row items-center justify-between p-4 bg-slate-800 font-bold"
  >
    <p>BASE Riddle Solver</p>
    <Web3Modal />
  </header>
  <main class="p-4 bg-neutral-900 overflow-auto h-full">
    <slot />
  </main>
</div>
