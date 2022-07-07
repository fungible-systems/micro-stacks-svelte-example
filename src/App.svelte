<script lang="ts">
  import {
    mountClient,
    getAuth,
    getOpenSignMessage,
watchStxAddress,
  } from "@micro-stacks/svelte";

  mountClient({
    appName: "New Remix App",
    appIconUrl: "https://remix.run/remix-v1.jpg",
  });

  const auth = getAuth();
  const openSignMessageValue = getOpenSignMessage();

  let message = "";
  let response: any;
  $: label = $auth.isRequestPending
    ? "Loading..."
    : $auth.isSignedIn
    ? "Sign out"
    : "Connect Stacks wallet";

  function onClick() {
    if ($auth.isSignedIn) {
      $auth.signOut();
    } else {
      $auth.authenticate();
    }
  }

  function onSignMessage() {
    $openSignMessageValue.openSignMessage({
      message,
      onFinish: (value) => (response = value),
    });
  }
  const stxAddress = watchStxAddress();

  $: sessionStatus = !$stxAddress ? 'No active session' : $stxAddress

</script>

<main>
  <h2>{sessionStatus}</h2>
  <button on:click={onClick}>
    {label}
  </button>

  {#if response}
    <pre>
        <code>{JSON.stringify(response, null, 2)}</code>
      </pre>
  {/if}

  <input bind:value={message} placeholder="Enter a message to sign!" />
  <button on:click={onSignMessage}> Sign message </button>
</main>

<style></style>
