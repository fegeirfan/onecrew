<script lang="ts">
  import { onMount } from "svelte";
  import { Plus, Trash2, ListCheck } from "lucide-svelte";

  interface TodoItem {
    id: number;
    text: string;
    completed: boolean;
  }

  let todos: TodoItem[] = [];
  let newTodoText: string = "";

  onMount(() => {
    const savedTodos = localStorage.getItem("checklist-app-todos");
    if (savedTodos) {
      todos = JSON.parse(savedTodos);
    }
  });

  $: {
    if (typeof localStorage !== "undefined") {
      localStorage.setItem("checklist-app-todos", JSON.stringify(todos));
    }
  }

  function addTodo() {
    if (!newTodoText.trim()) return;
    todos = [
      ...todos,
      { id: Date.now(), text: newTodoText.trim(), completed: false },
    ];
    newTodoText = "";
  }

  function deleteTodo(id: number) {
    todos = todos.filter((todo) => todo.id !== id);
  }

  function toggleCompletion(id: number) {
    todos = todos.map((todo) =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo,
    );
  }
</script>

<div
  class="card bg-base-100 shadow-md border border-base-200 max-w-2xl mx-auto"
>
  <div class="card-body">
    <h2 class="card-title text-2xl mb-6 text-primary">
      <ListCheck size={28} />
      Daftar Periksa Aktivitas
    </h2>

    <div class="join mb-6">
      <input
        type="text"
        class="input input-bordered join-item w-full focus:input-primary smooth-transition"
        bind:value={newTodoText}
        on:keydown={(e) => e.key === "Enter" && addTodo()}
        placeholder="Tambahkan aktivitas baru..."
      />
      <button
        class="btn btn-primary join-item btn-icon smooth-transition"
        on:click={addTodo}
      >
        <Plus size={18} />
        Tambah
      </button>
    </div>

    <div class="overflow-x-auto">
      {#if todos.length > 0}
        <table class="table w-full">
          <tbody>
            {#each todos as todo (todo.id)}
              <tr class="hover smooth-transition">
                <td class="w-12">
                  <label>
                    <input
                      type="checkbox"
                      class="checkbox checkbox-primary smooth-transition"
                      checked={todo.completed}
                      on:change={() => toggleCompletion(todo.id)}
                    />
                  </label>
                </td>
                <td
                  class={todo.completed
                    ? "line-through text-base-content/50"
                    : "text-base-content"}>{todo.text}</td
                >
                <td class="w-16">
                  <button
                    class="btn btn-ghost btn-xs btn-icon smooth-transition hover:text-error"
                    on:click={() => deleteTodo(todo.id)}
                  >
                    <Trash2 size={14} />
                    Hapus
                  </button>
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      {:else}
        <div class="text-center p-8 text-base-content/70">
          <ListCheck size={48} class="mx-auto mb-4 opacity-50" />
          <p>Belum ada aktivitas. Silakan tambahkan satu!</p>
        </div>
      {/if}
    </div>
  </div>
</div>
