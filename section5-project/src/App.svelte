<script>
    import meetups from './Meetups/meetups-store.js';
    import Header from "./UI/Header.svelte";
    import MeetupGrid from "./Meetups/MeetupGrid.svelte";
    import EditMeetup from './Meetups/EditMeetup.svelte';
    import Button from './UI/Button.svelte';
    import Error from './UI/Error.svelte';
    import MeetupDetail from './Meetups/MeetupDetail.svelte';
    import LoadingSpinner from './UI/LoadingSpinner.svelte';

    // let meetups = ;
    let pageData = {};
    let editedId;
    let editMode;
    let page = 'overview';
    let isLoading = true;
    let error;

    fetch('https://meetus--svelte-default-rtdb.firebaseio.com/meetups.json')
        .then(r => {
            if (!r.ok) {
                throw new Error('Fetching meetups failed, try again later')
            }
            return r.json()
        })
        .then(data => {
            const loadedMeetups = [];
            for (const key in data) {
                loadedMeetups.push({
                    ...data[key],
                    id: key
                });
            }
            setTimeout(() => {
                isLoading = false;
                meetups.setMeetups(loadedMeetups.reverse());
            }, 1000);
        }).catch(e => {
            error = e;
            isLoading = false;
            console.log(e)
        })

    function saveMeetup(e) {
        editMode = null;
        editedId = null;
    }

    function cancelEdit() {
        editMode = null;
        editedId = null;
    }

    function showDetails(e) {
        page = 'details';
        pageData.id = e.detail;
    }

    function closeDetails() {
        page = 'overview';
        pageData = {};
    }

    function startEdit(e) {
        editMode = 'edit';
        editedId = e.detail;
    }
    function clearError() {
        error = null;
    }
</script>

<style>
    main {
        margin-top: 5rem;
    }
</style>

{#if error}
    <Error message={error.message} on:cancel{clearError}/>
{/if}

<Header />

<main>
    {#if page === 'overview'}
        {#if editMode === 'edit'}
            <EditMeetup id={editedId} on:save={saveMeetup} on:cancel={cancelEdit} />
        {/if}
        {#if isLoading}
            <LoadingSpinner />
        {:else}
            <MeetupGrid
                meetups={$meetups}
                on:showDetails={showDetails}
                on:edit={startEdit}
                on:add={() => {editMode = 'edit'}}/>
        {/if}
    {:else}
        <MeetupDetail id={pageData.id} on:close={closeDetails} />
    {/if}
</main>
