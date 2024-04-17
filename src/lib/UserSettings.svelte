<script lang="ts">
  // Import libaries
  import { onMount } from 'svelte';

  // Global variables set to be used throughout User Settings
  let displayName: string = '';
  let email: string = '';
  let password: string = '';
  let isLoading: boolean = false;
  let errorMessage: string = '';

  // Function to fetch User Settings by looking through MySQL and retrieving the entry of the current User settings
  async function fetchUserSettings() {
    isLoading = true;
    try {
      const response = await fetch('/api/user/settings'); // replace with actual api from MySQL (Alfonso)
      if (!response.ok) {
        throw new Error('Failed to fetch settings');
      }
      const data = await response.json();
      displayName = data.displayName;
      email = data.email;
      isLoading = false;
    } catch (error) {
      errorMessage = 'Failed to load settings: ' + error.message;
      isLoading = false;
    }
  }

  // Function to commit changes to update the User Settings
  const updateSettings = async () => {
    isLoading = true;
    errorMessage = '';
    try {
      const response = await fetch('/api/user/settings', { //replace with actual api from MySQL (Alfonso)
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ displayName, email, password: password || undefined })
      });
      if (!response.ok) {
        throw new Error('Failed to update settings');
      }
      alert('Settings updated successfully!');
    } catch (error) {
      errorMessage = 'Failed to update settings: ' + error.message;
    }
    isLoading = false;
  };

  onMount(() => {
    fetchUserSettings();
  });
</script>

<style>
  .settings-container {
    display: flex;
    flex-direction: column;
    max-width: 300px;
    margin: auto;
    padding: 20px;
    box-shadow: 0 0 10px rgba(0,0,0,0.1);
  }
  .input-group {
    margin-bottom: 20px;
  }
  input, button {
    width: 100%;
    padding: 8px;
    margin-top: 5px;
  }
  .error-message {
    color: red;
    font-size: 0.9em;
  }
</style>

<div class="settings-container">
  <div class="input-group">
    <label for="displayName">Display Name:</label>
    <input type="text" id="displayName" bind:value={displayName}>
  </div>
  <div class="input-group">
    <label for="email">Email:</label>
    <input type="email" id="email" bind:value={email}>
  </div>
  <div class="input-group">
    <label for="password">New Password (leave blank to keep current):</label>
    <input type="password" id="password" bind:value={password}>
  </div>
  {#if errorMessage}
    <div class="error-message">{errorMessage}</div>
  {/if}
  <button on:click={updateSettings} disabled={isLoading}>
    {#if isLoading}
      Updating...
    {:else}
      Update Settings
    {/if}
  </button>
</div>
