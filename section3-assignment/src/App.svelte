<script>
	let password = '';
	let passwords = []
	const onEnter = e => {
		if (e.charCode === 13) savePassword();
	};

	function savePassword() {
		if (password.length < 5 || password.length > 10) {
			return
		}
		else {
			passwords = [password, ...passwords] // maybe use uuid
			password = '';
		}
	}

	function deletePass(pwd) {
		passwords = passwords.filter(p => !(p === pwd));
	}
</script>

<style>
	h2 {
		color: red;
		font-weight: bold;
	}
</style>

<div>
	<input
	type="password"
	placeholder="password"
	on:keypress={onEnter}
	bind:value={password} />

	<button on:click={savePassword}>Save</button>
</div>


{#if password.length < 5 && password.length > 0}
	<h2>Too short</h2>
{:else if password.length > 10}
	<h2>Too long</h2>
{:else}
	<p>{password}</p>
{/if}

<ul>
	{#each passwords as pwd}
		<li on:click={deletePass.bind(this, pwd)}>{pwd}</li>
	{/each}
</ul>
