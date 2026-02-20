<script lang="ts">
  import { createEventDispatcher } from "svelte";
  import {
    Home,
    Shuffle,
    BarChart2,
    ListCheck,
    Timer,
    NotepadText,
  } from "lucide-svelte";

  export let activePage: string = "main";
  const dispatch = createEventDispatcher();

  function navigate(page: string) {
    dispatch("navigate", page);
    // Close drawer on mobile after navigation
    const drawer = document.getElementById("main-drawer") as HTMLInputElement;
    if (drawer) drawer.checked = false;
  }
</script>

<ul
  class="menu p-4 w-80 min-h-full bg-base-200 text-base-content flex flex-col gap-2"
>
  <div class="mb-8 px-4 mt-4">
    <h1 class="text-3xl font-bold">
      <span class="text-primary">One</span><span class="text-secondary"
        >Crew</span
      >
    </h1>
    <p class="text-sm opacity-70 mt-1">Suite Utilitas All-in-One</p>
  </div>

  <li>
    <a
      href="#!"
      on:click={() => navigate("main")}
      class:active={activePage === "main"}
      class="py-3"
    >
      <Home size={20} /> Utama
    </a>
  </li>
  <li>
    <a
      href="#!"
      on:click={() => navigate("picker")}
      class:active={activePage === "picker"}
      class="py-3"
    >
      <Shuffle size={20} /> Pemilih
    </a>
  </li>
  <li>
    <a
      href="#!"
      on:click={() => navigate("stats")}
      class:active={activePage === "stats"}
      class="py-3"
    >
      <BarChart2 size={20} /> Statistik
    </a>
  </li>
  <li>
    <a
      href="#!"
      on:click={() => navigate("checklist")}
      class:active={activePage === "checklist"}
      class="py-3"
    >
      <ListCheck size={20} /> Checklist
    </a>
  </li>
  <li>
    <a
      href="#!"
      on:click={() => navigate("timer")}
      class:active={activePage === "timer"}
      class="py-3"
    >
      <Timer size={20} /> Timer
    </a>
  </li>
  <li>
    <a
      href="#!"
      on:click={() => navigate("notes")}
      class:active={activePage === "notes"}
      class="py-3"
    >
      <NotepadText size={20} /> Catatan
    </a>
  </li>
</ul>
