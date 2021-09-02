<script>
    import { createEventDispatcher } from 'svelte';

    export let title, price;


    const dispatch = createEventDispatcher();

    function addToCart() {
        dispatch('add-to-cart', {id: 'p1'})
    }

    function deleteProduct(e) {
        console.log(e.detail)
    }
</script>

<article >
    <h1>{title}</h1>
    <h2>${price}</h2>
    <button on:click={addToCart}>Add to Cart</button>
    <button on:click={deleteProduct}>Delete</button>
</article>
