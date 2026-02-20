<script lang="ts">
    import { Palette, X } from "lucide-svelte";

    let currentTheme: string = "ocean";

    const themes = [
        { name: "ocean", label: "Ocean", emoji: "🌊" },
        { name: "retro", label: "Retro", emoji: "🌅" },
        { name: "forest", label: "Forest", emoji: "🌲" },
        { name: "lavender", label: "Lavender", emoji: "💜" },
        { name: "dark", label: "Dark", emoji: "🌙" },
    ];

    function changeTheme(theme: string) {
        currentTheme = theme;
        document.documentElement.setAttribute("data-theme", theme);

        // Remove focus from the FAB to close it after selection
        if (document.activeElement instanceof HTMLElement) {
            document.activeElement.blur();
        }
    }

    // Set initial theme
    if (typeof document !== "undefined") {
        document.documentElement.setAttribute("data-theme", currentTheme);
    }
</script>

<div class="fab fab-flower fixed bottom-6 right-6 z-50">
    <!-- A focusable div with tabindex is necessary to work on all browsers. role="button" is necessary for accessibility -->
    <div
        tabindex="0"
        role="button"
        class="btn btn-circle btn-primary btn-lg shadow-xl border-primary-content border-2"
    >
        <Palette size={28} />
    </div>

    <!-- Main Action button replaces the original button when FAB is open -->
    <button
        class="fab-main-action btn btn-circle btn-primary btn-lg shadow-xl border-primary-content border-2"
        aria-label="Tutup Pilihan Tema"
    >
        <X size={28} />
    </button>

    <!-- Buttons that show up when FAB is open -->
    {#each themes.slice().reverse() as theme}
        <button
            class="btn btn-circle bg-base-100 border-base-300 shadow-md text-2xl hover:bg-base-200 transition-colors"
            class:ring-2={currentTheme === theme.name}
            class:ring-primary={currentTheme === theme.name}
            on:click={() => changeTheme(theme.name)}
            title={theme.label}
            aria-label="Ubah tema ke {theme.label}"
        >
            {theme.emoji}
        </button>
    {/each}
</div>
