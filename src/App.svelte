
<script lang="ts">
  import './app.css'; // Import global styles
  import { onMount } from 'svelte';
  import { quintOut } from 'svelte/easing';
  import { crossfade } from 'svelte/transition';

  import Nav from './lib/Nav.svelte';
  import Checklist from './lib/Checklist.svelte';
  import Timer from './lib/Timer.svelte';
  import Notes from './lib/Notes.svelte';
  import Export from './lib/Export.svelte';
  import Stats from './lib/Stats.svelte';
  import Picker from './lib/Picker.svelte';

  // --- Page Management ---
  let currentPage = 'main';

  function handleNavigate(event: any) {
      currentPage = event.detail;
      editingIndex = -1; // Reset editing state on page change
  }

  // --- State Management ---
  let names: string[] = [];
  let newNames: string = '';
  let groupMode: 'byCount' | 'byMembers' = 'byCount';
  let groupCount: number = 2;
  let membersPerGroup: number = 2;
  let groups: string[][] = [];
  
  // -- Editing State --
  let editingIndex: number = -1;
  let editingText: string = '';

  const [send, receive] = crossfade({
		duration: (d) => Math.sqrt(d * 200),
		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: (t) => `transform: ${transform} scale(${t}); opacity: ${t}`
			};
		}
	});

  // --- Lifecycle ---
  onMount(() => {
    const savedNames = localStorage.getItem('name-list-app-names');
    if (savedNames) {
      names = JSON.parse(savedNames);
    }
  });

  // --- Data Persistence ---
  $: {
    if (typeof localStorage !== 'undefined') {
      localStorage.setItem('name-list-app-names', JSON.stringify(names));
    }
  }

  // --- Name Management ---
  function addNames() {
    if (!newNames.trim()) return;
    const namesToAdd = newNames
      .split('\n')
      .map(name => name.trim())
      .filter(name => name.length > 0 && !names.includes(name));

    names = [...names, ...namesToAdd];
    newNames = '';
  }

  function deleteName(index: number) {
    names = names.filter((_, i) => i !== index);
    cancelEdit();
  }

  function clearAll() {
    // Using window.confirm - a simple modal can be a future improvement
    if (confirm('Apakah Anda yakin ingin menghapus semua nama?')) {
      names = [];
      groups = [];
      cancelEdit();
    }
  }

  // --- Edit Functions ---
  function startEditing(index: number, currentName: string) {
      editingIndex = index;
      editingText = currentName;
  }

  function saveEdit(index: number) {
      if (editingText.trim() && !names.includes(editingText.trim())) {
          names[index] = editingText.trim();
          names = names; // Trigger reactivity
      }
      cancelEdit();
  }

  function cancelEdit() {
      editingIndex = -1;
      editingText = '';
  }

  // --- Core Features (Grouping only) ---
  function generateGroups() {
    if (names.length === 0) return;
    let shuffledNames = [...names].sort(() => Math.random() - 0.5);
    let newGroups: string[][] = [];
    if (groupMode === 'byCount') {
        if (groupCount <= 0 || groupCount > shuffledNames.length) return;
        for (let i = 0; i < groupCount; i++) newGroups.push([]);
        let currentGroup = 0;
        shuffledNames.forEach(name => {
            newGroups[currentGroup].push(name);
            currentGroup = (currentGroup + 1) % groupCount;
        });
    } else { 
        if (membersPerGroup <= 0) return;
        for (let i = 0; i < shuffledNames.length; i += membersPerGroup) {
            newGroups.push(shuffledNames.slice(i, i + membersPerGroup));
        }
    }
    groups = newGroups;
  }

</script>

<main class="container mx-auto p-4 md:p-8">
  <header class="text-center mb-8">
    <h1 class="text-5xl font-bold text-primary mb-2">OneCrew</h1>
    <p class="text-xl text-base-content/80">Suite Utilitas All-in-One Anda</p>
    <Nav on:navigate={handleNavigate} />
  </header>

  <div class="content">
    {#if currentPage === 'main'}
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
          <!-- Kolom Kiri -->
          <div class="flex flex-col gap-8">
              <div class="card bg-base-100 shadow-xl">
                  <div class="card-body">
                    <h2 class="card-title">Manajemen Daftar</h2>
                    <textarea class="textarea textarea-bordered h-32" bind:value={newNames} placeholder="Contoh:\nAndi\nBudi\nCitra"></textarea>
                    <div class="card-actions justify-end">
                        <button class="btn btn-primary" on:click={addNames}>Tambahkan Nama</button>
                        {#if names.length > 0}
                            <button class="btn btn-error" on:click={clearAll}>Hapus Semua</button>
                        {/if}
                    </div>
                  </div>
              </div>

              {#if names.length > 0}
                <div class="card bg-base-100 shadow-xl">
                    <div class="card-body">
                        <h2 class="card-title">Daftar Nama ({names.length})</h2>
                        <div class="overflow-x-auto max-h-96">
                          <table class="table table-zebra w-full">
                            <tbody>
                              {#each names as name, index (name)}
                              <tr in:receive={{key: name}} out:send={{key: name}} class="hover">
                                  {#if editingIndex === index}
                                    <td colspan="2">
                                      <input class="input input-bordered input-sm w-full" type="text" bind:value={editingText} on:keydown={e => e.key === 'Enter' && saveEdit(index)} on:blur={() => saveEdit(index)} autoFocus />
                                    </td>
                                    <td>
                                      <button class="btn btn-sm btn-success" on:click={() => saveEdit(index)}>Simpan</button>
                                    </td>
                                  {:else}
                                    <th>{index + 1}</th>
                                    <td>{name}</td>
                                    <td class="text-right">
                                        <button class="btn btn-ghost btn-xs" on:click={() => startEditing(index, name)}>Edit</button>
                                        <button class="btn btn-ghost btn-xs text-error" on:click={() => deleteName(index)}>Hapus</button>
                                    </td>
                                  {/if}
                              </tr>
                              {/each}
                            </tbody>
                          </table>
                        </div>
                    </div>
                </div>
              {/if}
          </div>

          <!-- Kolom Kanan -->
          <div class="flex flex-col gap-8">
              {#if names.length > 0}
                <div class="card bg-base-100 shadow-xl">
                    <div class="card-body">
                        <h2 class="card-title">Pembagi Grup</h2>
                        <div class="join w-full">
                            <button class="btn join-item flex-grow" class:btn-active={groupMode === 'byCount'} on:click={() => groupMode = 'byCount'}>Jml Grup</button>
                            <button class="btn join-item flex-grow" class:btn-active={groupMode === 'byMembers'} on:click={() => groupMode = 'byMembers'}>Anggota/Grup</button>
                        </div>
                        <div class="form-control w-full">
                          {#if groupMode === 'byCount'}
                              <label class="label" for="groupCountInput"><span class="label-text">Berapa banyak grup?</span></label>
                              <input id="groupCountInput" type="number" bind:value={groupCount} min="1" max={names.length} placeholder="mis: 2" class="input input-bordered w-full" />
                          {:else}
                              <label class="label" for="membersPerGroupInput"><span class="label-text">Berapa banyak anggota per grup?</span></label>
                              <input id="membersPerGroupInput" type="number" bind:value={membersPerGroup} min="1" placeholder="mis: 3" class="input input-bordered w-full" />
                          {/if}
                        </div>
                        <div class="card-actions justify-end">
                            <button class="btn btn-primary" on:click={generateGroups} disabled={names.length < 2}>Buat Grup</button>
                        </div>
                    </div>
                </div>

                {#if groups.length > 0}
                    <div class="card bg-base-100 shadow-xl">
                      <div class="card-body">
                        <h2 class="card-title">Hasil Grup</h2>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            {#each groups as group, i}
                                <div class="card bg-base-200">
                                  <div class="card-body p-4">
                                    <h3 class="card-title text-base">Grup {i + 1}</h3>
                                    <ul class="list-disc list-inside">
                                        {#each group as name}<li>{name}</li>{/each}
                                    </ul>
                                  </div>
                                </div>
                            {/each}
                        </div>
                      </div>
                    </div>
                {/if}

                <div class="card bg-base-100 shadow-xl">
                  <div class="card-body">
                    <Export {names} {groups} />
                  </div>
                </div>
              {/if}
          </div>
      </div>
    {:else if currentPage === 'picker'}
        <Picker {names} {groups} />
    {:else if currentPage === 'stats'}
        <Stats {names} {groups} />
    {:else if currentPage === 'checklist'}
      <Checklist />
    {:else if currentPage === 'timer'}
      <Timer />
    {:else if currentPage === 'notes'}
      <Notes />
    {/if}
  </div>
</main>

<style>
  /* Style block is now empty or can be removed */
  /* All styles are handled by Tailwind/DaisyUI via app.css */
</style>
