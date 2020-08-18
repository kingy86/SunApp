<script>
  //Init
  let userObject = null;
  const userbase = window.userbase;
  let authPromise = userbase
    .init({ appId: "5c55b6f7-bd53-4707-877d-0e4a87d274c6" })
    .then(({ user }) => (userObject = user));

  //Auth
  let username, password;
  const signIn = () =>
    (authPromise = userbase
      .signIn({ username, password })
      .then(user => (userObject = user)));

  const signUp = () =>
    (authPromise = userbase
      .signUp({ username, password })
      .then(user => (userObject = user)));

  const signOut = () => {
    authPromise = userbase.signOut().then((userObject = null));
  };

  $: console.log(userObject);

  //Database
  let toDoPromise;
  let toDoList = [],
    newToDo = "";
  const databaseName = "toDoList";
  const changeHandler = items => {
    toDoList = items;
  };
  $: if (userObject)
    toDoPromise = userbase.openDatabase({ databaseName, changeHandler });
  const addToDo = item => {
    userbase.insertItem({ databaseName, item: newToDo });
    newToDo = "";
  };

  const deleteToDo = itemId => {
    toDoPromise = userbase.deleteItem({ databaseName, itemId });
  };
</script>

<style>
  .container {
    display: flex;
    justify-content: center;
  }
  form {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 50%;
  }
  .sign-out {
    position: absolute;
    top: 10px;
    right: 10px;
  }
  .signin-header {
    width: 100%;
  }
  .to-do-row {
    display: flex;
    flex-direction: row;
  }
</style>

{#await authPromise}
  <h3>Loading...</h3>
{:then _}
  {#if !userObject}
    <div class="container">
      <form>
        <label for="username">Username</label>
        <input id="username" type="text" bind:value={username} />
        <label for="password">Password</label>
        <input id="password" type="password" bind:value={password} />
        <button on:click={signUp} type="button">Sign Up</button>
        <button on:click={signIn} type="button">Sign In</button>
      </form>
    </div>
  {:else}
    <div class="signin-header">
      <h1>Hi, {username}!</h1>
      <button class="sign-out" on:click={signOut}>Sign Out</button>
    </div>
    {#await toDoPromise}
      <h3>Loading To Do List...</h3>
    {:then _}
      <div class="container">
        <label for="new-todo">Add Item</label>
        <input id="new-todo" type="text" bind:value={newToDo} />
        <button on:click={addToDo}>Add To Do</button>
      </div>
      {#each toDoList as { item, itemId }, index}
        <div class="to-do-row">
          <h4>{item}</h4>
          <button
            on:click={() => {
              deleteToDo(itemId);
            }}>
            X
          </button>
        </div>
      {/each}

    {:catch error}
      There was a problem {error}
    {/await}
  {/if}

{:catch error}
  There was a problem {error}
{/await}
