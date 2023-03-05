<script lang="ts">
  import { BASE_GOERLI_CHAIN_ID } from "$lib/constants";
  import { connected, chainId } from "svelte-ethers-store";

  import ConnectedPage from "./ConnectedPage.svelte";
  import DisconnectedPage from "./DisconnectedPage.svelte";
  import InvalidChainPage from "./InvalidChainPage.svelte";
</script>

{#if $connected}
  {#if $chainId == BASE_GOERLI_CHAIN_ID}
    <ConnectedPage />
  {:else}
    <InvalidChainPage />
  {/if}
{:else}
  <DisconnectedPage />
{/if}
