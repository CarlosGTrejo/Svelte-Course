<script>
	import { tick } from 'svelte';
	import Product from './Product.svelte';
	import Modal from './Modal.svelte';

	let products = [
		{
			id: 'p1',
			title: 'A book',
			price: 9.99
		}
	];

	let text ='Lorem ipsum dolor, sit amet consectetur adipisicing elit.';

	let showModal = false;
	let closable = false;

	function addToCart(e) {
		console.dir(e);
	}

	function deleteProduct(e) {
		console.dir(e);
	}

	function transform(e) {
		if (e.which !== 9) {
			return;
		}
		e.preventDefault();

		const selectionStart = e.target.selectionStart;
		const selectionEnd = e.target.selectionEnd;
		const value = e.target.value

		text = value.slice(0, selectionStart) +
			   value.slice(selectionStart, selectionEnd).toUpperCase() +
			   value.slice(selectionEnd);

		tick().then(() => {
			e.target.selectionStart = selectionStart;
			e.target.selectionEnd = selectionEnd;
		})
		// This DOESN'T WORK VVVV
		// e.target.selectionStart = selectionStart;
		// e.target.selectionEnd = selectionEnd;
	}
</script>

{#each products as product}
<Product
	{...product}
 	on:add-to-cart={addToCart}
	on:delete={deleteProduct}/>
{/each}

<button on:click="{() => showModal = true}">Show Modal</button>

{#if showModal}
<Modal
	on:cancel={() => showModal = false}
	on:close={() => showModal = false}
	let:didAgree={closable}>
	<h1 slot='header'>Hello!</h1>
	<p>This actually does not work</p>
	<button slot="footer" on:click={() => showModal = false} disabled={!closable}>confirm</button>
</Modal>
{/if}

<textarea rows="5" value={text} on:keydown={transform}></textarea>
