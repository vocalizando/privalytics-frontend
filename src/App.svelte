<script>
  import Entry from "./lib/Entry.svelte";
  // FIXME: Add dep to package.json
  import {retrieveAllEntries} from "./lib/privalytics-js.js"

  const hostname = localStorage.getItem("hostname") || prompt("Hostname")
  const username = localStorage.getItem("username") || prompt("Username")
  const token = localStorage.getItem("token") || prompt("Token")

  if (hostname == null || username == null || token == null) {
    alert("Invalid credentials")
  }

  let promise = retrieveAllEntries(hostname, {
    username,
    token,
  })

  promise.then(() => {
    localStorage.setItem("hostname", hostname)
    localStorage.setItem("username", username)
    localStorage.setItem("token", token)
  })

  function resetCredentials() {
    localStorage.removeItem("hostname")
    localStorage.removeItem("username")
    localStorage.removeItem("token")

    alert("The credentials are now removed from local storage")
  }
</script>

<main>
  <h1>Privalytics frontend</h1>

  <button on:click|once|preventDefault|trusted={() => resetCredentials()}>Reset credentials</button>

  {#await promise}
    <p>Loading entries...</p>
  {:then entries}
    <ul>
      {#each entries as entry (entry.metadata.duid)}
        <Entry entry={entry} user_data={{ username, token }} hostname={hostname}/>
      {/each}
    </ul>
  {:catch error}
    <p>Failed (check console for details)</p>
  {/await}
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    padding: 1em;
    margin: 0 auto;
  }

  button {
    background-color: #dc3545;
    border: none;
    color: white;
    padding: 10px 12px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    border-radius: 10px;
    font-size: 15px;
  }

  button:hover {
    cursor: pointer;
  }
</style>
