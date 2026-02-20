
# OneCrew - Blueprint

## Overview

OneCrew is an all-in-one utility suite designed to streamline common tasks. It provides a set of tools to help users manage lists of names, create random groups, pick random names, track statistics, manage a checklist, use a timer/stopwatch, and take quick notes. The application is built with Svelte and Vite, and it uses Daisy UI and Tailwind CSS for a modern and responsive user interface.

## Project Structure & Features

### Styling & Design

*   **UI Framework:** Daisy UI and Tailwind CSS are used for all styling. This provides a consistent, modern, and responsive design across the application.
*   **Theme:** The application uses the default Daisy UI theme, which can be easily customized.
*   **Layout:** The application uses a responsive grid layout that adapts to different screen sizes. The main page has a two-column layout on larger screens, and a single-column layout on smaller screens.
*   **Components:** All components have been migrated to use Daisy UI classes for a consistent look and feel. This includes buttons, forms, cards, tables, and navigation.

### Core Features

*   **Name Management:**
    *   Add names to a list from a textarea.
    *   Edit and delete names from the list.
    *   Clear the entire list.
*   **Group Generator:**
    *   Generate random groups from the list of names.
    *   Two modes for group generation: by number of groups, or by number of members per group.
*   **Random Picker:**
    *   Pick a specified number of random names from the list or from a specific group.
*   **Statistics:**
    *   View statistics about the list of names, including total names, total characters, longest and shortest name length, number of groups, and average members per group.
*   **Checklist:**
    *   A simple to-do list to keep track of tasks.
*   **Timer/Stopwatch:**
    *   A combination of a countdown timer and a stopwatch.
*   **Notes:**
    *   A simple notes area for quick note-taking, with automatic saving to local storage.
*   **Export:**
    *   Export the list of names and groups to a text file or JSON.

## Current Change: Migration to Daisy UI

The current change involved a complete migration of the application's styling from a custom CSS solution to Daisy UI and Tailwind CSS.

### Plan & Steps

1.  **Install Daisy UI and Tailwind CSS:**
    *   Installed `daisyui` and `tailwindcss` as dev dependencies.
    *   Configured `tailwind.config.js` to include Daisy UI.
    *   Created a `postcss.config.js` file.
    *   Created an `app.css` file to import Tailwind CSS and apply base styles.
2.  **Update `vite.config.ts`:**
    *   No changes were needed for `vite.config.ts` in this case.
3.  **Refactor Svelte Components:**
    *   **`Nav.svelte`:** Replaced the custom navigation with a Daisy UI `navbar`.
    *   **`Picker.svelte`:** Replaced the form and button with Daisy UI `form-control`, `select`, and `btn` components.
    *   **`Stats.svelte`:** Replaced the statistics list with a Daisy UI `stats` component.
    *   **`Checklist.svelte`:** Replaced the checklist with a Daisy UI table and form components.
    *   **`Timer.svelte`:** Replaced the timer with Daisy UI components, including `tabs`, `countdown`, and `btn`.
    *   **`Notes.svelte`:** Replaced the notes area with a Daisy UI `card` and `textarea`.
    *   **`App.svelte`:**
        *   Imported the new `app.css` file.
        *   Replaced the main layout with a responsive grid using Tailwind CSS classes.
        *   Replaced all remaining custom styles with Daisy UI and Tailwind CSS utility classes.
        *   Removed the old `<style>` block.
4.  **Update Dependencies:**
    *   Ran `npm install` to ensure all dependencies were up-to-date.
    *   Ran `npm audit fix --force` to address security vulnerabilities.
5.  **Final Review:**
    *   Reviewed the application to ensure all components are styled correctly and the layout is responsive.
    *   All custom CSS has been removed, and the application now relies entirely on Daisy UI and Tailwind CSS for its styling.
