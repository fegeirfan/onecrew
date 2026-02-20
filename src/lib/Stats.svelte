<script lang="ts">
    import { Users, Zap, TrendingUp, TrendingDown } from "lucide-svelte";

    export let names: string[] = [];
    export let groups: string[][] = [];

    // Reactive statistics
    $: totalNames = names.length;
    $: numberOfGroups = groups.length;
    $: avgMembers =
        numberOfGroups > 0 ? (totalNames / numberOfGroups).toFixed(1) : 0;
    $: shortestNameLength =
        names.length > 0 ? Math.min(...names.map((n) => n.length)) : 0;
    $: longestNameLength =
        names.length > 0 ? Math.max(...names.map((n) => n.length)) : 0;
    $: totalChars = names.reduce((acc, name) => acc + name.length, 0);
</script>

{#if totalNames > 0}
    <div
        class="card bg-base-100 shadow-md border border-base-200 max-w-4xl mx-auto"
    >
        <div class="card-body">
            <h2 class="card-title text-2xl mb-6 text-primary">
                Statistik Daftar Nama
            </h2>

            <div
                class="stats stats-vertical lg:stats-horizontal shadow w-full bg-base-200/50"
            >
                <div class="stat">
                    <div class="stat-figure text-primary">
                        <Users size={32} />
                    </div>
                    <div class="stat-title">Total Nama</div>
                    <div class="stat-value text-primary">{totalNames}</div>
                    <div class="stat-desc">Nama dalam daftar</div>
                </div>

                <div class="stat">
                    <div class="stat-figure text-secondary">
                        <Zap size={32} />
                    </div>
                    <div class="stat-title">Total Karakter</div>
                    <div class="stat-value text-secondary">{totalChars}</div>
                    <div class="stat-desc">Di semua nama</div>
                </div>

                <div class="stat">
                    <div class="stat-figure text-success">
                        <TrendingUp size={32} />
                    </div>
                    <div class="stat-title">Nama Terpanjang</div>
                    <div class="stat-value text-success">
                        {longestNameLength}
                    </div>
                    <div class="stat-desc">Karakter</div>
                </div>

                <div class="stat">
                    <div class="stat-figure text-warning">
                        <TrendingDown size={32} />
                    </div>
                    <div class="stat-title">Nama Terpendek</div>
                    <div class="stat-value text-warning">
                        {shortestNameLength}
                    </div>
                    <div class="stat-desc">Karakter</div>
                </div>
            </div>

            {#if numberOfGroups > 0}
                <div
                    class="stats stats-vertical lg:stats-horizontal shadow w-full mt-6 bg-base-200/50"
                >
                    <div class="stat">
                        <div class="stat-figure text-accent">
                            <Users size={32} />
                        </div>
                        <div class="stat-title">Jumlah Grup</div>
                        <div class="stat-value text-accent">
                            {numberOfGroups}
                        </div>
                        <div class="stat-desc">Grup yang dibuat</div>
                    </div>

                    <div class="stat">
                        <div class="stat-figure text-info">
                            <Users size={32} />
                        </div>
                        <div class="stat-title">Rata-rata Anggota</div>
                        <div class="stat-value text-info">{avgMembers}</div>
                        <div class="stat-desc">per Grup</div>
                    </div>
                </div>
            {/if}
        </div>
    </div>
{:else}
    <div role="alert" class="alert alert-info max-w-2xl mx-auto shadow-md">
        <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            class="stroke-current shrink-0 w-6 h-6"
            ><path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
            ></path></svg
        >
        <span
            >Tambahkan beberapa nama di halaman Utama untuk melihat statistik.</span
        >
    </div>
{/if}
