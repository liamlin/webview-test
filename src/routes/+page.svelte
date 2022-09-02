<script>
  let messages = [
    { from: 'RN', text: 'Hi' },
    { from: 'WEB', text: 'svelte-preprocesssvelte-preprocesssvelte-preprocesssvelte-preprocesssvelte-p' },
  ]
  let myMessage = ''
  let sending = false
  let errorMsg = ''
  let errorTimer = null
  function showErrorMessage (message) {
    if (errorTimer) {
      clearTimeout(errorTimer)
      errorTimer = null
    }
    errorMsg = message
    errorTimer = setTimeout(() => {
      errorMsg = ''
      errorTimer = null
    }, 3000)
  }
  function send () {
    if (window.ReactNativeWebView) {
      sending = true
      window.ReactNativeWebView.postMessage(myMessage)
    } else {
      showErrorMessage('RN WebView Not Available!')
    }
  }
  window.addEventListener('rn-message', (payload) => {
    const { type, message } = payload
    if (type === 'ack') {
      messages.push({ from: 'WEB', text: myMessage })
      myMessage = ''
      sending = false
    } else if (message) {
      messages.push({ from: 'RN', text: message })
    }
  })
</script>

<div class="w-full h-screen border-2 max-w-[500px] mx-auto flex flex-col">
  <div class="w-full text-center text-2xl py-3">Cross Platform Chat Room</div>
  <div class="flex flex-col flex-1 overflow-auto">
    {#each messages as message}
    <div class="flex flex-col p-2 max-w-[50%] w-fit" class:rn={message.from === 'RN'} class:web={message.from === 'WEB'}>
      <div class="text-gray-500">{message.from}</div>
      <div class="text-lg border-2 rounded-2xl break-all p-3 message-text">{message.text}</div>
    </div>
    {/each}
  </div>
  <div class="flex mt-auto h-[60px] relative">
    {#if errorMsg}
      <div class="absolute left-0 right-0 -top-[50px] text-center text-xl opacity-80 p-2 text-red-600">{errorMsg}</div>
    {/if}
    <input type="text" class="px-4 flex-1 bg-amber-300" autofocus bind:value={myMessage}>
    <button type="button" class="border-t-2 px-10" on:click={send} disabled={sending || !myMessage}>Send</button>
  </div>
</div>

<style lang="scss">
  .rn {
      @apply self-start;
      .message-text {
        @apply bg-blue-100;
      }
  }
  .web {
      @apply self-end text-right;
    .message-text {
      @apply bg-green-100;
    }
  }
  .message-text {
      @apply text-xl;
  }
  button:disabled {
    @apply bg-gray-300 opacity-50;
  }
</style>
