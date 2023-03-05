<script lang="ts" context="module">
  evm.attachContract(
    "BASERiddle",
    "0xc1e40f9FD2bc36150e2711e92138381982988791",

    JSON.stringify(riddleABI)
  );
</script>

<script lang="ts">
  import riddleABI from "$lib/abi/riddle_abi.json";
  import { defaultEvmStores as evm } from "svelte-ethers-store";
  import { connected } from "svelte-ethers-store";

  function triggerDisconnect() {
    evm.disconnect();
  }
  function triggerConnect() {
    evm.setProvider();
  }
</script>

<div class="bg-neutral-900 p-2 rounded-md">
  {#if $connected}
    <button on:click="{triggerDisconnect}">Disconnnect Wallet</button>
  {:else}
    <button on:click="{triggerConnect}">Connect Wallet</button>
  {/if}
</div>
