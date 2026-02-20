
<script lang="ts">
  import { onMount } from "svelte";

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
</script>

<div class="card bg-base-100 shadow-xl max-w-2xl mx-auto">
  <div class="card-body">
    <div class="flex justify-between items-center mb-4">
        <h2 class="card-title text-2xl">Catatan Cepat</h2>
        <div class="badge badge-outline badge-lg">{saveStatus}</div>
    </div>
    
    <textarea
        class="textarea textarea-bordered textarea-lg w-full h-48 resize-none"
        bind:value={notes}
        on:input={handleInput}
        rows="8"
        placeholder="Tulis catatan Anda di sini. Akan tersimpan secara otomatis."
    ></textarea>

    <div class="card-actions justify-end mt-4">
        <div class="text-sm text-base-content/60">
            Penyimpanan otomatis saat Anda berhenti mengetik.
        </div>
    </div>
  </div>
</div>
