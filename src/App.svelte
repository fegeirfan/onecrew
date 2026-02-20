<script lang="ts">
  import "./app.css"; // Import global styles
  import { onMount } from "svelte";
  import { quintOut } from "svelte/easing";
  import { crossfade } from "svelte/transition";
  import { Plus, Trash2, Pencil, Check, Users } from "lucide-svelte";

  import Nav from "./lib/Nav.svelte";
  import Checklist from "./lib/Checklist.svelte";
  import Timer from "./lib/Timer.svelte";
  import Notes from "./lib/Notes.svelte";
  import Export from "./lib/Export.svelte";
  import Stats from "./lib/Stats.svelte";
  import Picker from "./lib/Picker.svelte";
  import ThemeFab from "./lib/ThemeFab.svelte";

  // --- Page Management ---
  let currentPage = "main";

  function handleNavigate(event: any) {
    currentPage = event.detail;
    editingIndex = -1; // Reset editing state on page change
  }

  // --- State Management ---
  let names: string[] = [];
  let newNames: string = "";
  let groupMode: "byCount" | "byMembers" = "byCount";
  let groupCount: number = 2;
  let membersPerGroup: number = 2;
  let groups: string[][] = [];

  // -- Editing State --
  let editingIndex: number = -1;
  let editingText: string = "";

  const [send, receive] = crossfade({
    duration: (d) => Math.sqrt(d * 200),
    fallback(node, params) {
      const style = getComputedStyle(node);
      const transform = style.transform === "none" ? "" : style.transform;

      return {
        duration: 600,
        easing: quintOut,
        css: (t) => `transform: ${transform} scale(${t}); opacity: ${t}`,
      };
    },
  });

  // --- Lifecycle ---
  onMount(() => {
    const savedNames = localStorage.getItem("name-list-app-names");
    if (savedNames) {
      names = JSON.parse(savedNames);
    }
  });

  // --- Data Persistence ---
  $: {
    if (typeof localStorage !== "undefined") {
      localStorage.setItem("name-list-app-names", JSON.stringify(names));
    }
  }

  // --- Name Management ---
  function addNames() {
    if (!newNames.trim()) return;
    const namesToAdd = newNames
      .split("\n")
      .map((name) => name.trim())
      .filter((name) => name.length > 0 && !names.includes(name));

    names = [...names, ...namesToAdd];
    newNames = "";
  }

  function deleteName(index: number) {
    names = names.filter((_, i) => i !== index);
    cancelEdit();
  }

  function clearAll() {
    // Using window.confirm - a simple modal can be a future improvement
    if (confirm("Apakah Anda yakin ingin menghapus semua nama?")) {
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
    editingText = "";
  }

  // --- Core Features (Grouping only) ---
  function generateGroups() {
    if (names.length === 0) return;
    let shuffledNames = [...names].sort(() => Math.random() - 0.5);
    let newGroups: string[][] = [];
    if (groupMode === "byCount") {
      if (groupCount <= 0 || groupCount > shuffledNames.length) return;
      for (let i = 0; i < groupCount; i++) newGroups.push([]);
      let currentGroup = 0;
      shuffledNames.forEach((name) => {
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

<div class="drawer lg:drawer-open">
  <input id="main-drawer" type="checkbox" class="drawer-toggle" />

  <div class="drawer-content flex flex-col min-h-screen bg-base-100">
    <!-- Navbar (Mobile only) -->
    <div
      class="w-full navbar bg-base-100 border-b border-base-200 lg:hidden sticky top-0 z-40 shadow-sm"
    >
      <div class="flex-none">
        <label
          for="main-drawer"
          aria-label="open sidebar"
          class="btn btn-square btn-ghost"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            class="inline-block w-6 h-6 stroke-current"
            ><path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 6h16M4 12h16M4 18h16"
            ></path></svg
          >
        </label>
      </div>
      <div class="flex-1 px-2 mx-2 text-xl font-bold">
        <span class="text-primary">One</span><span class="text-secondary"
          >Crew</span
        >
      </div>
    </div>

    <!-- Main Content -->
    <main class="flex-1 p-4 md:p-8 overflow-y-auto">
      <div class="max-w-6xl mx-auto content animate-in fade-in duration-500">
        <!-- Title Header (Desktop Only) -->
        <div class="hidden lg:block mb-8">
          <h2 class="text-3xl font-bold text-base-content capitalize">
            {#if currentPage === "main"}
              Utama
            {:else if currentPage === "picker"}
              Pemilih Acak
            {:else if currentPage === "stats"}
              Statistik
            {:else if currentPage === "checklist"}
              Checklist
            {:else if currentPage === "timer"}
              Timer
            {:else if currentPage === "notes"}
              Catatan
            {/if}
          </h2>
          <div class="divider mt-2 mb-0"></div>
        </div>

        {#if currentPage === "main"}
          <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
            <!-- Kolom Kiri -->
            <div class="flex flex-col gap-8">
              <div class="card bg-base-100 shadow-md border border-base-200">
                <div class="card-body">
                  <h2 class="card-title text-primary">
                    <Users size={20} /> Manajemen Daftar
                  </h2>
                  <textarea
                    class="textarea textarea-bordered h-32 focus:textarea-primary transition-colors"
                    bind:value={newNames}
                    placeholder="Contoh:&#10;Andi&#10;Budi&#10;Citra"
                  ></textarea>
                  <div class="card-actions justify-end mt-2">
                    <button class="btn btn-primary" on:click={addNames}>
                      <Plus size={18} /> Tambahkan Nama
                    </button>
                    {#if names.length > 0}
                      <button
                        class="btn btn-error btn-outline"
                        on:click={clearAll}
                      >
                        <Trash2 size={18} /> Hapus Semua
                      </button>
                    {/if}
                  </div>
                </div>
              </div>

              {#if names.length > 0}
                <div class="card bg-base-100 shadow-md border border-base-200">
                  <div class="card-body">
                    <h2 class="card-title text-secondary">
                      <ListCheck size={20} />
                      Daftar Nama ({names.length})
                    </h2>
                    <div
                      class="overflow-x-auto max-h-96 mt-2 rounded-box border border-base-200"
                    >
                      <table
                        class="table table-zebra table-sm md:table-md w-full"
                      >
                        <thead>
                          <tr>
                            <th>No</th>
                            <th>Nama</th>
                            <th class="text-right">Aksi</th>
                          </tr>
                        </thead>
                        <tbody>
                          {#each names as name, index (name)}
                            <tr
                              in:receive={{ key: name }}
                              out:send={{ key: name }}
                              class="hover"
                            >
                              {#if editingIndex === index}
                                <td colspan="2">
                                  <input
                                    class="input input-bordered input-sm w-full focus:input-primary"
                                    type="text"
                                    bind:value={editingText}
                                    on:keydown={(e) =>
                                      e.key === "Enter" && saveEdit(index)}
                                    on:blur={() => saveEdit(index)}
                                    autoFocus
                                  />
                                </td>
                                <td class="text-right">
                                  <button
                                    class="btn btn-sm btn-success btn-square"
                                    on:click={() => saveEdit(index)}
                                  >
                                    <Check size={14} />
                                  </button>
                                </td>
                              {:else}
                                <th class="w-12 text-base-content/50"
                                  >{index + 1}</th
                                >
                                <td class="font-medium">{name}</td>
                                <td class="text-right">
                                  <button
                                    class="btn btn-ghost btn-sm btn-square"
                                    on:click={() => startEditing(index, name)}
                                  >
                                    <Pencil size={16} />
                                  </button>
                                  <button
                                    class="btn btn-ghost btn-sm btn-square text-error"
                                    on:click={() => deleteName(index)}
                                  >
                                    <Trash2 size={16} />
                                  </button>
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
                <div class="card bg-base-100 shadow-md border border-base-200">
                  <div class="card-body">
                    <h2 class="card-title text-accent">
                      <Users size={20} />
                      Pembagi Grup
                    </h2>
                    <div class="join w-full mt-2">
                      <button
                        class="btn join-item flex-grow"
                        class:btn-active={groupMode === "byCount"}
                        on:click={() => (groupMode = "byCount")}
                      >
                        Jumlah Grup
                      </button>
                      <button
                        class="btn join-item flex-grow"
                        class:btn-active={groupMode === "byMembers"}
                        on:click={() => (groupMode = "byMembers")}
                      >
                        Anggota/Grup
                      </button>
                    </div>
                    <div class="form-control w-full mt-4">
                      {#if groupMode === "byCount"}
                        <label class="label pt-0" for="groupCountInput">
                          <span class="label-text font-medium"
                            >Berapa banyak grup?</span
                          >
                        </label>
                        <input
                          id="groupCountInput"
                          type="number"
                          bind:value={groupCount}
                          min="1"
                          max={names.length}
                          placeholder="mis: 2"
                          class="input input-bordered w-full focus:input-primary"
                        />
                      {:else}
                        <label class="label pt-0" for="membersPerGroupInput">
                          <span class="label-text font-medium"
                            >Berapa banyak anggota per grup?</span
                          >
                        </label>
                        <input
                          id="membersPerGroupInput"
                          type="number"
                          bind:value={membersPerGroup}
                          min="1"
                          placeholder="mis: 3"
                          class="input input-bordered w-full focus:input-primary"
                        />
                      {/if}
                    </div>
                    <div class="card-actions justify-end mt-4">
                      <button
                        class="btn btn-primary"
                        on:click={generateGroups}
                        disabled={names.length < 2}
                      >
                        Buat Grup
                      </button>
                    </div>
                  </div>
                </div>

                {#if groups.length > 0}
                  <div
                    class="card bg-base-100 shadow-md border border-base-200"
                  >
                    <div class="card-body">
                      <h2 class="card-title text-success">Hasil Grup</h2>
                      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-2">
                        {#each groups as group, i}
                          <div
                            class="card bg-base-200 shadow-sm border border-base-300"
                          >
                            <div class="card-body p-4">
                              <h3
                                class="card-title text-base text-primary mb-2"
                              >
                                Grup {i + 1}
                              </h3>
                              <ul class="space-y-1">
                                {#each group as name}
                                  <li class="flex items-center gap-2 text-sm">
                                    <div
                                      class="w-1.5 h-1.5 rounded-full bg-primary/50"
                                    ></div>
                                    {name}
                                  </li>
                                {/each}
                              </ul>
                            </div>
                          </div>
                        {/each}
                      </div>
                    </div>
                  </div>
                {/if}

                <div class="card bg-base-100 shadow-md border border-base-200">
                  <div class="card-body">
                    <Export {names} {groups} />
                  </div>
                </div>
              {/if}
            </div>
          </div>
        {:else if currentPage === "picker"}
          <Picker {names} {groups} />
        {:else if currentPage === "stats"}
          <Stats {names} {groups} />
        {:else if currentPage === "checklist"}
          <Checklist />
        {:else if currentPage === "timer"}
          <Timer />
        {:else if currentPage === "notes"}
          <Notes />
        {/if}
      </div>
    </main>

    <ThemeFab />
  </div>

  <!-- Sidebar -->
  <div class="drawer-side z-50 shadow-lg">
    <label for="main-drawer" aria-label="close sidebar" class="drawer-overlay"
    ></label>
    <Nav on:navigate={handleNavigate} activePage={currentPage} />
  </div>
</div>

<style>
  /* Base styles applied via Tailwind and DaisyUI */
</style>
