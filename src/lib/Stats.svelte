
<script lang="ts">
    export let names: string[] = [];
    export let groups: string[][] = [];

    // Reactive statistics
    $: totalNames = names.length;
    $: numberOfGroups = groups.length;
    $: avgMembers = numberOfGroups > 0 ? (totalNames / numberOfGroups).toFixed(1) : 0;
    $: shortestNameLength = names.length > 0 ? Math.min(...names.map(n => n.length)) : 0;
    $: longestNameLength = names.length > 0 ? Math.max(...names.map(n => n.length)) : 0;
    $: totalChars = names.reduce((acc, name) => acc + name.length, 0);

</script>

{#if totalNames > 0}
    <div class="card bg-base-100 shadow-xl max-w-4xl mx-auto">
        <div class="card-body">
            <h2 class="card-title text-2xl mb-6">Statistik Daftar Nama</h2>

            <div class="stats stats-vertical lg:stats-horizontal shadow w-full">
                <div class="stat">
                    <div class="stat-figure text-primary">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="inline-block w-8 h-8 stroke-current"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"></path></svg>
                    </div>
                    <div class="stat-title">Total Nama</div>
                    <div class="stat-value text-primary">{totalNames}</div>
                    <div class="stat-desc">Nama dalam daftar</div>
                </div>

                <div class="stat">
                    <div class="stat-figure text-secondary">
                         <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="inline-block w-8 h-8 stroke-current"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
                    </div>
                    <div class="stat-title">Total Karakter</div>
                    <div class="stat-value text-secondary">{totalChars}</div>
                    <div class="stat-desc">Di semua nama</div>
                </div>

                 <div class="stat">
                    <div class="stat-title">Nama Terpanjang</div>
                    <div class="stat-value">{longestNameLength}</div>
                    <div class="stat-desc">Karakter</div>
                </div>

                <div class="stat">
                    <div class="stat-title">Nama Terpendek</div>
                    <div class="stat-value">{shortestNameLength}</div>
                    <div class="stat-desc">Karakter</div>
                </div>
            </div>

            {#if numberOfGroups > 0}
                <div class="stats stats-vertical lg:stats-horizontal shadow w-full mt-6">
                    <div class="stat">
                        <div class="stat-title">Jumlah Grup</div>
                        <div class="stat-value">{numberOfGroups}</div>
                        <div class="stat-desc">Grup yang dibuat</div>
                    </div>

                    <div class="stat">
                        <div class="stat-title">Rata-rata Anggota</div>
                        <div class="stat-value">{avgMembers}</div>
                        <div class="stat-desc">per Grup</div>
                    </div>
                </div>
            {/if}
        </div>
    </div>
{:else}
    <div role="alert" class="alert alert-info max-w-2xl mx-auto">
      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-current shrink-0 w-6 h-6"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
      <span>Tambahkan beberapa nama di halaman Utama untuk melihat statistik.</span>
    </div>
{/if}
