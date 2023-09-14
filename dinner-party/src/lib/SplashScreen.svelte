<script>
	import { showSplashScreen } from "$lib/stores";

	let password = '';
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

	function handleSubmit() {
		// Check if the password is correct
		if (password === '1234') {
			showSplashScreen.set(false); // Hide the splash screen
      if (typeof localStorage !== 'undefined') {
        localStorage.setItem('savedName', name);
        localStorage.setItem('savedEmail', email);
      }
		} else {
      // flash error message
      alert('They must not like you very much.');
    }
	}

  loadSavedData();
</script>

<div class="splash-screen">
	<h1>Welcome to Our Page</h1>
	<p>Please enter the password to access the page:</p>
	<form on:submit={handleSubmit}>
    <label>
      Password:
      <input type="password" bind:value={password} required />
    </label>
		<label>
			Name:
			<input type="text" bind:value={name} required />
		</label>
		<label>
			Email:
			<input type="email" bind:value={email} required />
		</label>
		<button type="submit">Submit</button>
	</form>
</div>
