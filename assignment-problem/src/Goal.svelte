<script>
    export let goal = '';
</script>

<style>
    .red-text {
        color: red;
    }
</style>

<h1 class:red-text={!!~goal.indexOf('!')}>{goal}</h1>
