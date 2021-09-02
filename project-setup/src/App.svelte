<script>
	import ContactCard from './ContactCard.svelte';
	let name = 'World';
	let age = 69;

	let job, desc, image;

	function incrementAge() {
		age++;
	}
</script>

<style>
	h1 {
		color: purple;
	}
</style>

<h1>Hello {name}, my age is {age}!</h1>
<button on:click={incrementAge}>Change Age</button>

<!-- <input type="text" value={name} on:input={nameInput} /> -->
<!-- A more concise way of writing ^^^ is vvv -->
<input type="text" bind:value={name} />

<div>
	<br/><br/>
	<label for="job-title">Job Title:</label>
	<input type="text" id="job-title" placeholder="Job Title" bind:value={job}/> <br/><br/>
</div>
<div>
	<label for="desc">Description</label>
	<textarea style="vertical-align: top;" id="desc" rows="3" placeholder="A short description" bind:value={desc}></textarea>
</div>
<input type="text" placeholder="image url" bind:value={image} />


<ContactCard
	userName={name}
	jobTitle={job}
	description={desc}
	userImage={image}/>
