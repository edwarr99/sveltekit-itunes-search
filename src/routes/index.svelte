<script>
    import supabase from '$lib/db'

    let newName
    let newDescription
    let submit = false

    async function getData() {
        const { data, error } = await supabase
            .from('todos')
            .select()

        if (error) throw new Error(error.message)

        console.log(data)

        return data

    }

    async function sendData() {

      const { data, error } = await supabase
        .from('todos')
        .insert([
          { 'name': newName,
            'description': newDescription
          }
        ])
      if (error) throw new Error(error.message)
      return data
    }
  
</script>

    <!-- Display current Todos -->
    {#await getData()}
      <p>Fetching data...</p>

    {:then data}
      <table>
      {#each data as todo}
        <tr>
          <td>{todo.name}</td>
          <td>{todo.description}</td>
        </tr>
      {/each}
      </table>

    {:catch error}
      <p>Something went wrong while fetching the data:</p>
      <pre>{error}</pre>

    {/await}


    <!-- Submit new Todo -->
    <form on:submit|preventDefault={() => submit = true}>
      <input type="text" bind:value={newName}>
      <input type="text" bind:value={newDescription}>
      <input type="submit" value="Submit" on:click={() => submit = false}>
    </form>

    {#if submit}
      {#await sendData()}
        <p>Sending data...</p>
      {:then data}
        <p>Succesfully sent data.</p>
      {:catch error}
        <p>Something went wrong while sending the data:</p>
        <pre>{error}</pre>
      {/await}
    {/if}
