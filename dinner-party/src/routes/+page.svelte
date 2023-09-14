<script>
  import { writable } from 'svelte/store';

  import { host, showSplashScreen } from '$lib/stores';

  import SplashScreen from '$lib/SplashScreen.svelte';

  let hostName = ''; // name of the host(s), not a networking term

  host.subscribe((value) => {
    hostName = value;
  });

  const dinnerParties = writable([]);
  let newDate = '';
  let newTime = '';
  let newDescription = '';
  let name = '';
  let email = '';

  function loadSavedData() {
    if (typeof localStorage !== 'undefined') {
      const savedName = localStorage.getItem('savedName');
      const savedEmail = localStorage.getItem('savedEmail');

      if (savedName) name = savedName;
      if (savedEmail) email = savedEmail;
    }
  }

  function addNewParty(event) {
    event.preventDefault();
    dinnerParties.update((parties) => {
      return [
        ...parties,
        {
          date: newDate,
          time: newTime,
          attendees: [],
          description: newDescription,
          slots: 10
        }
      ];
    });
  }

  function signUpForParty(party) {
    // check if there are slots still available
    if (party.slots <= party.attendees.length) {
      alert('Sorry, dinner party is full.');
    } else {
      if (name.length === 0) {
        name = prompt('Please enter your name to sign up.');
        return;
      }
      if (name) {
        party.attendees.push(name);
        party.slots--; // decrement the slots available
        // send signal to update the UI
        dinnerParties.update((parties) => {
          return [...parties];
        });
      }
    }
  }

  loadSavedData();
</script>

{#if $showSplashScreen}
  <SplashScreen />
{/if}
{#if !$showSplashScreen}
  <h1>Welcome to {hostName}'s dinner party homepage!</h1>
  <p>Hello, {name}!</p>
  <h2>Future Dates</h2>
  <ul>
    {#each $dinnerParties as party}
      <li data-key={party.key}>
        <div>Date: {party.date}</div>
        <div>Time: {party.time}</div>
        <div>Slots: {party.slots}</div>
        <div>Attendees: {party.attendees.join(', ')}</div>
        <div>Description: {party.description}</div>
        <button disabled={party.slots === 0} on:click={() => signUpForParty(party)}>Sign Up</button>
      </li>
    {/each}
  </ul>

  <h2>Suggest A Date</h2>
  <form on:submit={addNewParty}>
    <label>
      Date:
      <input type="date" bind:value={newDate} />
    </label>
    <label>
      Time:
      <input type="time" bind:value={newTime} />
    </label>
    <label>
      Description:
      <textarea bind:value={newDescription} rows="4" />
    </label>
    <button type="submit">Add New Date</button>
  </form>
{/if}
