<script lang="ts">
  import { logged, intra, currentChat, currentPage, cookie } from './stores.js';
  import Router from 'svelte-spa-router';
  import Chat from './routes/Chat.svelte';
  import Home from './routes/Home.svelte';
  import NotFound from './routes/NotFound.svelte';
  import Profile from './routes/Profile.svelte';
  import UserProfile from './routes/UserProfile.svelte';
  import Pong from './routes/Pong.svelte';
  import { writable } from 'svelte/store';
  import { onMount, beforeUpdate } from 'svelte';
  import { Socket } from 'socket.io-client';
  import io from 'socket.io-client';

  let socket = null;

  import { FRONTEND_URL, BACKEND_URL} from './domain.js'

  export let name: string = 'transcendance';

  const kuki: string = $cookie;

  let routes: any = {
    '/': Home,
    '/pong': Pong,
    '/chat': Chat,
    '/profile': Profile,
    '/userprofile': UserProfile,
    '/*': NotFound,
  };

  async function logOut(): Promise<void> {
    if ($intra == 'true') {
      logged.update((n) => 'false');
      intra.update((n) => 'false');
      localStorage.removeItem('currentChat');
      localStorage.removeItem('currentPage');
      localStorage.removeItem('invitedPlayer');
      for(let key in localStorage) {
        delete localStorage[key];
    }
      let cookies: string[] = document.cookie?.split(';');

      for (let i:number = 0; i < cookies.length; i++) {
        const cookie:string = cookies[i];
        const eqPos: number = cookie.indexOf('=');
        const name_cookie: string = eqPos > -1 ? cookie.substr(0, eqPos) : cookie;
        document.cookie = name_cookie + '=;expires=Thu, 01 Jan 1970 00:00:00 GMT';
      }

      await fetch(`${BACKEND_URL}/users/logout`, {
        method: 'POST',
        headers: {
          Authorization: 'Bearer ' + kuki,
          'Content-type': 'application/json; charset=UTF-8',
        },
      });
      location.reload();
      alert('✅ You successfully logged out');
    }
  }

  onMount(() => {
  });
</script>

<svelte:body />
<main>
  <img src="img/pong.svg" style="width: 300px;" alt="Pong icon" />
  <nav class="menu">
    {#if $logged == 'true'}
      {#if $currentPage == 'home'}
        <a
          on:click={() => {
            currentPage.update((n) => 'home');
          }}
          class="item_active"
          href="#/">HOME</a
        >
      {:else}
        <a
          on:click={() => {
            currentPage.update((n) => 'home');
          }}
          class="item"
          href="#/">HOME</a
        >
      {/if}

      {#if $currentPage == 'profile'}
        <a
          on:click={() => {
            currentPage.update((n) => 'profile');
          }}
          class="item_active"
          href="#/profile">PROFILE</a
        >
      {:else}
        <a
          on:click={() => {
            currentPage.update((n) => 'profile');
          }}
          class="item"
          href="#/profile">PROFILE</a
        >
      {/if}

      {#if $currentPage == 'chat'}
        <a
          on:click={() => {
            currentPage.update((n) => 'chat');
          }}
          class="item_active"
          href="#/chat">CHAT</a
        >
      {:else}
        <a
          on:click={() => {
            currentPage.update((n) => 'chat');
          }}
          class="item"
          href="#/chat">CHAT</a
        >
      {/if}

      {#if $currentPage == 'pong'}
        <a
          on:click={() => {
            currentPage.update((n) => 'pong');
          }}
          class="item_active"
          href="#/pong">PONG</a
        >
      {:else}
        <a
          on:click={() => {
            currentPage.update((n) => 'pong');
          }}
          class="item"
          href="#/pong">PONG</a
        >
      {/if}

      <a class="item" on:click={logOut} href="#/">LOGOUT</a>
    {:else}
      <a class="item" href="#/">HOME</a>
      <a class="item" href="#/">PONG</a>
      <a class="item" href="#/">PROFILE</a>
      <a class="item" href="#/">CHAT</a>
      <a class="item" on:click={logOut} href="#/">LOGOUT</a>
    {/if}
  </nav>
</main>
<Router {routes} />

<style>
:global(body) {
  position: relative;
margin: 0 auto;
padding: 0px;
width: auto;
height: 100%;
display: block;
min-width: 500px;
    }

  main {
    text-align: center;
    padding: 1em;
    min-width: 500px;
    margin: 0 auto;
    display: block;
    min-width: 500px;
    align-items: center;
    align-content: center;
    justify-content: center;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS',
      sans-serif;
  }

  .menu {
    align-items: center;
    margin: 20px auto 0;
  }
  .item {
    font-size: 12px;
    text-align: center;
    display: inline-block;
    padding: 5px 20px;
    color: rgb(255, 255, 255);
    background-color: hsl(0, 0%, 44%);
  }
  .item_active {
    font-size: 12px;
    text-align: center;
    display: inline-block;
    padding: 5px 20px;
    color: rgb(255, 255, 255);
    background-color: darkslategray;
  }

</style>
