<script lang="ts">
  import { onMount } from "svelte";
  import { signerAddress, signer, contracts } from "svelte-ethers-store";
  import { ethers } from "ethers";

  import { writable } from "svelte/store";

  import riddle1 from "$lib/img/riddle1.png";
  import riddle2 from "$lib/img/riddle2.png";
  import riddle3 from "$lib/img/riddle3.png";

  var currentChallenge = writable<number | null>(null);

  var riddle1Solution: string = "";
  var riddle2Solution: string = "";
  var riddle3Solution: string = "";

  const trueSolution1 = "Faucet";
  const trueSolution2 = "The Merge";
  const trueSolution3 = "EIP-4844";

  async function solveChallenge1() {
    await $contracts["BASERiddle"].solveChallenge1(trueSolution1);
  }
  async function solveChallenge2() {
    const answerHash = ethers.utils.keccak256(
      ethers.utils.toUtf8Bytes(trueSolution2)
    );

    const signedAnswer = await $signer.signMessage(
      ethers.utils.arrayify(answerHash)
    );

    await $contracts["BASERiddle"].solveChallenge2(trueSolution2, signedAnswer);
    await loadChallengeLevel();
  }
  async function solveChallenge3() {
    const answerHash = ethers.utils.keccak256(
      ethers.utils.toUtf8Bytes(trueSolution3)
    );

    const wallet = ethers.Wallet.createRandom();

    const signedAnswer = await wallet.signMessage(
      ethers.utils.arrayify(answerHash)
    );

    const sig = ethers.utils.splitSignature(signedAnswer);

    await $contracts["BASERiddle"].solveChallenge3(
      trueSolution3,
      wallet.address,
      sig.compact
    );
    await $contracts["BASERiddle"].solveChallenge3(
      trueSolution3,
      wallet.address,
      signedAnswer
    );
    await loadChallengeLevel();
  }

  onMount(async () => {
    await loadChallengeLevel();
  });

  async function loadChallengeLevel() {
    const s1: boolean = await $contracts["BASERiddle"].hasSolvedChallenge1(
      $signerAddress
    );
    const s2: boolean = await $contracts["BASERiddle"].hasSolvedChallenge2(
      $signerAddress
    );
    const s3: boolean = await $contracts["BASERiddle"].isOnLeaderboard(
      $signerAddress
    );

    currentChallenge.set(!s1 ? 1 : !s2 ? 2 : !s3 ? 3 : 4);
    //currentChallenge.set(4);
  }
</script>

<div class="flex flex-row justify-center h-full w-full">
  {#if $currentChallenge == null}
    <p>Loading your current riddle status</p>
  {:else}
    <div
      class="table p-4 bg-neutral-700 h-full rounded-lg justify-center w-1/2 relative"
    >
      <div
        class:hidden="{$currentChallenge != 1}"
        class="flex flex-col items-center justify-between  gap-3"
      >
        <div>
          <h1 class="font-bold text-2xl">Challenge 1</h1>
          <img class="p-3 w-full" src="{riddle1}" alt="Code of first riddle" />
        </div>

        <div
          class="w-full grid grid-cols-1 text-center gap-2 px-16 py-4 bg-neutral-500 rounded-lg text-black"
        >
          <h2 class="font-bold text-xl">Riddle Solution</h2>

          <p>
            Just answer the question, the solution is <b class="text-white"
              >{trueSolution1}</b
            >
          </p>
        </div>

        <button
          class="border bg-blue-500 px-5 py-2 rounded-sm hover:bg-blue-600"
          on:click="{solveChallenge1}">Solve</button
        >
      </div>
      <div
        class:hidden="{$currentChallenge != 2}"
        class="flex flex-col items-center justify-between gap-3"
      >
        <div>
          <h1 class="font-bold text-2xl">Challenge 2</h1>
          <img class="p-3 w-full" src="{riddle2}" alt="Code of second riddle" />
        </div>

        <div
          class="w-full grid grid-cols-1 text-center gap-2 px-16 py-4 bg-neutral-500 rounded-lg text-black"
        >
          <h2 class="font-bold text-xl">Riddle Solution</h2>

          <p>This one consists of 2 steps:</p>

          <ul class="list-decimal">
            <li>
              Answer the question, the solution is <b class="text-white"
                >{trueSolution2}</b
              >
            </li>
            <li>Sign the hash of the solution with your private key</li>
          </ul>

          <p class="mt-6 font-bold">
            The signature needs to be created by the message sender, hence this
            will ask you to sign some bytes: The hash of "The Merge"
          </p>
        </div>

        <button
          class="border bg-blue-500 px-5 py-2 rounded-sm hover:bg-blue-600"
          on:click="{solveChallenge2}">Solve</button
        >
      </div>
      <div
        class:hidden="{$currentChallenge != 3}"
        class="flex flex-col items-center justify-between gap-3"
      >
        <div>
          <h1 class="font-bold text-2xl">Challenge 3</h1>
          <img class="p-3 w-full" src="{riddle3}" alt="Code of third riddle" />
        </div>

        <div
          class="w-full grid grid-cols-1 text-center gap-2 px-16 py-4 bg-neutral-500 rounded-lg text-black"
        >
          <h2 class="font-bold text-xl">Riddle Solution</h2>

          <p>This one consists of 2 steps:</p>

          <ul class="list-decimal">
            <li>
              Answer the question, the solution is <b class="text-white"
                >{trueSolution3}</b
              >
            </li>
            <li>
              Create 2 different signatures that both sign the same message and
              are signed by the same signer
            </li>
          </ul>

          <p>
            The contract used to check the signature allows both, the old r,s,v
            signature format (65 bytes), as well as the new compact r,sv
            signature format (64 bytes). Submitted both of them does the trick!
          </p>

          <p class="mt-6 font-bold">
            This challenge requires 2 signatures but allows to specify the
            signer. This site internally generated a new wallet and created the
            signatures with that. However there are 2 transaction required, one
            for each signature!
          </p>
        </div>

        <button
          class="border bg-blue-500 px-5 py-2 rounded-sm hover:bg-blue-600"
          on:click="{solveChallenge3}">Solve</button
        >
      </div>
      <div class:hidden="{$currentChallenge != 4}" class="text-center">
        <h1 class="font-bold text-2xl">Congratulations!</h1>

        <h2>You have completed all riddles!</h2>

        <p class="p-4 text-9xl">🥳</p>
      </div>
    </div>
  {/if}
</div>
