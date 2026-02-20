<script lang="ts">
  import { onMount, onDestroy } from "svelte";
  import {
    Play,
    Square,
    RotateCcw,
    Timer as TimerIcon,
    Clock,
  } from "lucide-svelte";

  let time: number = 0; // dalam detik
  let timerRunning: boolean = false;
  let stopwatchRunning: boolean = false;
  let timerInterval: any;
  let stopwatchInterval: any;

  let timerInputMinutes: number = 5;

  function formatTime(seconds: number): string {
    const h = Math.floor(seconds / 3600)
      .toString()
      .padStart(2, "0");
    const m = Math.floor((seconds % 3600) / 60)
      .toString()
      .padStart(2, "0");
    const s = (seconds % 60).toString().padStart(2, "0");
    return `${h}:${m}:${s}`;
  }

  // --- Timer ---
  function startTimer() {
    if (timerInputMinutes > 0 && !timerRunning) {
      time = timerInputMinutes * 60;
      timerRunning = true;
      timerInterval = setInterval(() => {
        time--;
        if (time <= 0) {
          stopTimer();
          // Bisa tambahkan notifikasi di sini
        }
      }, 1000);
    }
  }

  function stopTimer() {
    clearInterval(timerInterval);
    timerRunning = false;
  }

  function resetTimer() {
    stopTimer();
    time = timerInputMinutes * 60;
  }

  // --- Stopwatch ---
  function startStopwatch() {
    if (!stopwatchRunning) {
      stopwatchRunning = true;
      stopwatchInterval = setInterval(() => {
        time++;
      }, 1000);
    }
  }

  function stopStopwatch() {
    clearInterval(stopwatchInterval);
    stopwatchRunning = false;
  }

  function resetStopwatch() {
    stopStopwatch();
    time = 0;
  }

  // --- Combined Controls ---
  function handleStart() {
    if (activeTab === "timer") startTimer();
    else startStopwatch();
  }

  function handleStop() {
    if (activeTab === "timer") stopTimer();
    else stopStopwatch();
  }

  function handleReset() {
    if (activeTab === "timer") resetTimer();
    else resetStopwatch();
  }

  onDestroy(() => {
    clearInterval(timerInterval);
    clearInterval(stopwatchInterval);
  });

  let activeTab: "timer" | "stopwatch" = "timer";

  function selectTab(tab: "timer" | "stopwatch") {
    activeTab = tab;
    time = 0; // Reset time on tab switch
    stopTimer();
    stopStopwatch();
    if (tab === "timer") {
      time = timerInputMinutes * 60;
    }
  }

  $: isRunning =
    (activeTab === "timer" && timerRunning) ||
    (activeTab === "stopwatch" && stopwatchRunning);
  $: isStopped = !isRunning;
</script>

<div class="card bg-base-100 shadow-md border border-base-200 max-w-md mx-auto">
  <div class="card-body items-center text-center">
    <h2 class="card-title text-2xl mb-4 text-primary">
      {#if activeTab === "timer"}
        <TimerIcon size={28} />
      {:else}
        <Clock size={28} />
      {/if}
      {activeTab === "timer" ? "Timer" : "Stopwatch"}
    </h2>

    <div role="tablist" class="tabs tabs-boxed mb-6">
      <a
        role="tab"
        class="tab smooth-transition"
        class:tab-active={activeTab === "timer"}
        on:click={() => selectTab("timer")}
      >
        <TimerIcon size={16} class="mr-2" />
        Timer
      </a>
      <a
        role="tab"
        class="tab smooth-transition"
        class:tab-active={activeTab === "stopwatch"}
        on:click={() => selectTab("stopwatch")}
      >
        <Clock size={16} class="mr-2" />
        Stopwatch
      </a>
    </div>

    <div class="text-6xl md:text-7xl font-mono font-extrabold my-8">
      <span class="countdown">
        <span style="--value:{Math.floor(time / 3600)};" class="text-primary"
        ></span>:
        <span
          style="--value:{Math.floor((time % 3600) / 60)};"
          class="text-secondary"
        ></span>:
        <span style="--value:{time % 60};" class="text-accent"></span>
      </span>
    </div>

    {#if activeTab === "timer"}
      <div class="form-control w-full max-w-xs mb-4">
        <label class="label" for="timer-input">
          <span class="label-text font-semibold">Setel durasi dalam menit</span>
        </label>
        <input
          type="number"
          id="timer-input"
          bind:value={timerInputMinutes}
          min="1"
          on:change={resetTimer}
          class="input input-bordered w-full max-w-xs focus:input-primary smooth-transition"
          disabled={isRunning}
        />
      </div>
    {/if}

    <div class="card-actions justify-center gap-4">
      <button
        class="btn btn-success btn-wide btn-icon smooth-transition"
        on:click={handleStart}
        disabled={isRunning}
      >
        <Play size={18} />
        Mulai
      </button>
      <button
        class="btn btn-warning btn-icon smooth-transition"
        on:click={handleStop}
        disabled={isStopped}
      >
        <Square size={18} />
        Stop
      </button>
      <button
        class="btn btn-error btn-icon smooth-transition"
        on:click={handleReset}
      >
        <RotateCcw size={18} />
        Reset
      </button>
    </div>
  </div>
</div>
