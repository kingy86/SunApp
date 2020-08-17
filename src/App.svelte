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
  let toDoList = [],
    newToDo = "";
  const dataBaseName = "toDoList";
  const changeHandler = items => {
    toDoList = items;
  };
  $: if (userObject) userbase.openDatabase({ dataBaseName, changeHandler });
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
    <div class="container">
      <h1>Hi, {username}!</h1>
      <button class="sign-out" on:click={signOut}>Sign Out</button>
      <label for="new-todo">New To Do</label>
      <input id="new-todo" type="text" bind:value={newToDo} />
      <button on:click={addToDo}>Add Today</button>
    </div>
  {/if}

{:catch error}
  There was a problem {error}
{/await}
