<script lang="ts">
  import { Shuffle, AlertCircle } from "lucide-svelte";

  export let names: string[] = [];
  export let groups: string[][] = [];

  let source: "all" | number = "all"; // 'all' or group index
  let count: number = 1;
  let picked: string[] = [];
  let error: string = "";

  function pickNames() {
    error = "";
    picked = [];
    let sourceList: string[] = [];

    if (source === "all") {
      sourceList = [...names];
    } else if (groups[source]) {
      sourceList = [...groups[source]];
    }

    if (sourceList.length === 0) {
      error = "Sumber yang dipilih tidak memiliki nama untuk dipilih.";
      return;
    }
    if (count > sourceList.length) {
      error = `Tidak dapat memilih ${count} nama karena hanya ada ${sourceList.length} nama yang tersedia.`;
      return;
    }

    let shuffled = sourceList.sort(() => 0.5 - Math.random());
    picked = shuffled.slice(0, count);
  }

  // Reset input when source changes
  $: {
    count = 1;
    picked = [];
    error = "";
  }
</script>

<div
  class="card bg-base-100 shadow-md border border-base-200 max-w-2xl mx-auto"
>
  <div class="card-body">
    <h2 class="card-title text-2xl mb-4 text-primary">
      <Shuffle size={28} />
      Pemilih Acak Lanjutan
    </h2>

    <div class="form-control w-full mb-4">
      <label class="label" for="source-select">
        <span class="label-text font-semibold">Pilih Sumber</span>
      </label>
      <select
        id="source-select"
        class="select select-bordered focus:select-primary smooth-transition"
        bind:value={source}
      >
        <option value="all">Semua Nama ({names.length})</option>
        {#if groups.length > 0}
          <optgroup label="Grup">
            {#each groups as group, index}
              <option value={index}>Grup {index + 1} ({group.length})</option>
            {/each}
          </optgroup>
        {/if}
      </select>
    </div>

    <div class="form-control w-full mb-4">
      <label class="label" for="pick-count">
        <span class="label-text font-semibold">Jumlah yang akan Dipilih</span>
      </label>
      <input
        id="pick-count"
        type="number"
        class="input input-bordered focus:input-primary smooth-transition"
        bind:value={count}
        min="1"
        placeholder="mis: 1"
      />
    </div>

    <div class="card-actions justify-end">
      <button
        class="btn btn-primary btn-icon smooth-transition"
        on:click={pickNames}
        disabled={names.length === 0}
      >
        <Shuffle size={18} />
        Pilih Nama
      </button>
    </div>

    {#if error}
      <div role="alert" class="alert alert-error mt-6">
        <AlertCircle size={24} />
        <span>{error}</span>
      </div>
    {/if}

    {#if picked.length > 0}
      <div class="mt-8">
        <h3 class="text-xl font-bold mb-4 text-secondary">Hasil Pilihan:</h3>
        <ul
          class="list-none p-0 m-0 grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4"
        >
          {#each picked as name, i}
            <li
              class="bg-base-200 p-4 rounded-lg shadow hover-lift smooth-transition text-center"
            >
              <div class="font-bold text-lg text-primary">{name}</div>
              <div class="text-sm text-base-content/70">Pilihan #{i + 1}</div>
            </li>
          {/each}
        </ul>
      </div>
    {/if}
  </div>
</div>
