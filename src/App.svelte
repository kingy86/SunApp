<script>
  const userbase = window.userbase;
  userbase.init({ appId: "5c55b6f7-bd53-4707-877d-0e4a87d274c6" });
  let username, password;

  const signIn = () => userbase.signIn({ username, password });
  const signUp = () => userbase.signUp({ username, password });
</script>

<form>
  <label for="username">Username</label>
  <input id="username" type="text" bind:value={username} />
  <label for="password">Password</label>
  <input id="password" type="password" bind:value={password} />
  <button on:click={signUp} type="button">Sign Up</button>
  <button on:click={signIn} type="button">Sign In</button>
</form>
