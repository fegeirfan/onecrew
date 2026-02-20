<script lang="ts">
  import { onMount } from "svelte";
  import { NotepadText, CheckCircle, Clock } from "lucide-svelte";

  let notes: string = "";
  let saveStatus: string = "Tersimpan";
  let debounceTimer: any;

  onMount(() => {
    const savedNotes = localStorage.getItem("notes-app-content");
    if (savedNotes) {
      notes = savedNotes;
    }
  });

  function handleInput() {
    saveStatus = "Menyimpan...";
    clearTimeout(debounceTimer);
    debounceTimer = setTimeout(() => {
      localStorage.setItem("notes-app-content", notes);
      saveStatus = "Tersimpan";
    }, 700); // Auto-save after 700ms of inactivity
  }

  $: isSaving = saveStatus === "Menyimpan...";
</script>

<div
  class="card bg-base-100 shadow-md border border-base-200 max-w-2xl mx-auto"
>
  <div class="card-body">
    <div class="flex justify-between items-center mb-4">
      <h2 class="card-title text-2xl text-primary">
        <NotepadText size={28} />
        Catatan Cepat
      </h2>
      <div
        class="badge badge-lg gap-2"
        class:badge-success={!isSaving}
        class:badge-warning={isSaving}
      >
        {#if isSaving}
          <Clock size={16} />
        {:else}
          <CheckCircle size={16} />
        {/if}
        {saveStatus}
      </div>
    </div>

    <textarea
      class="textarea textarea-bordered textarea-lg w-full h-96 resize-none focus:textarea-primary smooth-transition"
      bind:value={notes}
      on:input={handleInput}
      rows="8"
      placeholder="Tulis catatan Anda di sini. Akan tersimpan secara otomatis."
    ></textarea>

    <div class="card-actions justify-end mt-4">
      <div class="text-sm text-base-content/60 flex items-center gap-2">
        <CheckCircle size={16} />
        Penyimpanan otomatis saat Anda berhenti mengetik.
      </div>
    </div>
  </div>
</div>
