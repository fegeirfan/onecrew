
<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  let activePage: string = 'main';

  function navigate(page: string) {
    activePage = page;
    dispatch('navigate', page);
  }
</script>

<div class="navbar bg-base-100 rounded-box shadow-lg mb-8">
  <div class="navbar-start">
    <div class="dropdown">
      <label tabindex="0" class="btn btn-ghost lg:hidden">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h8m-8 6h16" /></svg>
      </label>
      <ul tabindex="0" class="menu menu-sm dropdown-content mt-3 z-[1] p-2 shadow bg-base-100 rounded-box w-52">
        <li><a href="#!" on:click={() => navigate('main')} class:active={activePage === 'main'}>Utama</a></li>
        <li><a href="#!" on:click={() => navigate('picker')} class:active={activePage === 'picker'}>Pemilih</a></li>
        <li><a href="#!" on:click={() => navigate('stats')} class:active={activePage === 'stats'}>Statistik</a></li>
        <li><a href="#!" on:click={() => navigate('checklist')} class:active={activePage === 'checklist'}>Checklist</a></li>
        <li><a href="#!" on:click={() => navigate('timer')} class:active={activePage === 'timer'}>Timer</a></li>
        <li><a href="#!" on:click={() => navigate('notes')} class:active={activePage === 'notes'}>Catatan</a></li>
      </ul>
    </div>
    <a href="#!" class="btn btn-ghost normal-case text-xl">OneCrew</a>
  </div>
  <div class="navbar-center hidden lg:flex">
    <ul class="menu menu-horizontal px-1">
      <li><a href="#!" on:click={() => navigate('main')} class:btn-active={activePage === 'main'} class="btn btn-ghost">Utama</a></li>
      <li><a href="#!" on:click={() => navigate('picker')} class:btn-active={activePage === 'picker'} class="btn btn-ghost">Pemilih</a></li>
      <li><a href="#!" on:click={() => navigate('stats')} class:btn-active={activePage === 'stats'} class="btn btn-ghost">Statistik</a></li>
       <li class="ml-4"><a href="#!" on:click={() => navigate('checklist')} class:btn-active={activePage === 'checklist'} class="btn btn-sm btn-outline">Checklist</a></li>
      <li><a href="#!" on:click={() => navigate('timer')} class:btn-active={activePage === 'timer'} class="btn btn-sm btn-outline">Timer</a></li>
      <li><a href="#!" on:click={() => navigate('notes')} class:btn-active={activePage === 'notes'} class="btn btn-sm btn-outline">Catatan</a></li>
    </ul>
  </div>
  <div class="navbar-end">
    <!-- Bisa ditambahkan item di sini nanti -->
  </div>
</div>

<!-- Style block is not needed anymore thanks to Daisy UI -->
