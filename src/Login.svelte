<script>
  import {createEventDispatcher} from 'svelte';
  const dispatch = createEventDispatcher();

  let password = 'password';
  let username = 'mvolkmann';

  const login = () => dispatch('login');
  const signup = () => dispatch('signup');
</script>

<section>
  <form on:submit|preventDefault={login}>
    <label>
      Username
      <input bind:value={username} />
    </label>
    <label>
      Password
      <input type="password" bind:value={password} />
    </label>
    <div class="buttons">
      <button disabled={!username || !password}>Login</button>
      <button type="button" on:click={signup}>Sign Up</button>
    </div>
  </form>
</section>

<style>
  .buttons {
    display: flex;
    justify-content: space-between;

    font-size: 24px;
    margin-top: 1rem;
  }

  form {
    display: inline-block;
  }

  input {
    display: block;
    margin-top: 5px;
  }

  label {
    display: block;
    font-size: 24px;
    margin-top: 0.5rem;
  }
</style>
