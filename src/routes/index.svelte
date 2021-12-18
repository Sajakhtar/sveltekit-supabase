<script>
  import supabase from '$lib/db'

  let games = ''

  async function getData() {
    const { data, error } = await supabase
      .from('games')
      .select()
    if (error) throw new Error(error.message)

    games = data
    return data
  }

  let newGame
  let submit = false

  async function sendData() {
    const { data, error } = await supabase
      .from('games')
      .insert([
        { 'title': newGame }
      ]);

    if (error) throw new Error(error.message)
    getData()
    return data;
  }

</script>

<h1>My favorite games</h1>

<!-- Send Data to DB -->
<form on:submit|preventDefault={() => submit = true}>
  <input type="text" bind:value={newGame}>
  <input type="submit" value="Submit" on:click={() => submit = false}>
</form>

{#if submit}
  {#await sendData()}
    <p>Sending data...</p>
  {:then data}
    <p>Successfully sent data.</p>
  {:catch error}
    <p>Something went wrong while sending the data:</p>
    <pre>{error}</pre>
  {/await}
{/if}



<!-- Fetch Data from DB -->
{#await getData()}
  <p>Fetching data...</p>
{:then data}
  {#each games as game}
    <li>{game.title}</li>
  {/each}
{:catch error}
  <p>Something went wrong while fetching the data:</p>
  <pre>{error}</pre>
{/await}
