<script>
    import { createEventDispatcher } from 'svelte';
    import TextInput from '../UI/TextInput.svelte'
    import Button from '../UI/Button.svelte';
    import Modal from '../UI/Modal.svelte';
    import { isEmpty, isValidEmail } from '../helpers/validation.js';
    import meetups from './meetups-store.js';
    import MeetupDetail from './MeetupDetail.svelte';

    export let id = null;


    let title = '',
    subtitle = '',
    address = '',
    email = '',
    description = '',
    imageUrl = '';

    if (id) {
        const unsubscribe = meetups.subscribe(items => {
            // SelectedMeetup
            ({title, subtitle, address, email, description, imageUrl} = items.find(i => i.id === id));
        });

        unsubscribe();
    }

    const dispatch = createEventDispatcher();

    $: titleValid = !isEmpty(title);
    $: subtitleValid = !isEmpty(subtitle);
    $: addressValid = !isEmpty(address);
    $: emailValid = isValidEmail(email);
    $: descriptionValid = !isEmpty(description);
    $: imageUrlValid = !isEmpty(imageUrl);
    $: formIsValid =
        titleValid &&
        subtitleValid &&
        addressValid &&
        emailValid &&
        descriptionValid &&
        imageUrlValid;

    function submitForm() {
        const newMeetup = {
            title,
            subtitle,
            address,
            email,
            description,
            imageUrl
        };

        // meetups.push(newMeetup);  // DOES NOT WORK!
        if (id) {
            fetch(`https://meetus--svelte-default-rtdb.firebaseio.com/meetups/${id}.json`, {
                method: 'PATCH',
                body: JSON.stringify({...newMeetup}),
                headers: {'Content-Type': 'application/json'}
            }).then(r => {
                if (!r.ok) {
                    throw new Error('An error occurred, please try again!');
                }
                meetups.updateMeetup(id, newMeetup);
            }).catch(e => {console.log(e);});
        } else {
            fetch('https://meetus--svelte-default-rtdb.firebaseio.com/meetups.json', {
                method: 'POST',
                body: JSON.stringify({...newMeetup, isFavorite: false}),
                headers: {'Content-Type': 'application/json'}
            }).then(r => {
                if (!r.ok) {
                    throw new Error('An error occured, please try again!');
                }
                return r.json();
            }).then(data => {
                meetups.addMeetup({
                    ...newMeetup,
                    isFavorite: false,
                    id: data.name
                });
            }).catch(err => console.log(err))
        }
        dispatch('save');
    }

    function deleteMeetup() {
        fetch(`https://meetus--svelte-default-rtdb.firebaseio.com/meetups/${id}.json`, {
            method: 'DELETE'
        }).then(r => {
            if (!r.ok) {
                throw new Error('An error occurred, try again!')
            }
        }).catch(e => console.log(e))
        meetups.removeMeetup(id);
        dispatch('save');
    }

    function cancel() {
        dispatch('cancel');
    }
</script>

<style>
    form {
        width: 100%;
    }
</style>


<Modal title='Edit Meetup Data' on:cancel>
    <form on:submit|preventDefault={submitForm}>
        <TextInput
            id="title"
            label="Title"
            value={title}
            valid={titleValid}
            validityMessage='Please enter a valid title.'
            on:input={e => (title = e.target.value)} />
        <TextInput
            id="subtitle"
            label="Subtitle"
            value={subtitle}
            valid={subtitleValid}
            validityMessage='Please enter a valid subtitle.'
            on:input={e => (subtitle = e.target.value)} />
        <TextInput
            id="address"
            label="address"
            value={address}
            valid={addressValid}
            validityMessage='Please enter a valid address.'
            on:input={e => (address = e.target.value)} />
        <TextInput
            id="imageUrl"
            label="Image URL"
            value={imageUrl}
            valid={imageUrlValid}
            validityMessage='Please enter a valid image URL.'
            on:input={e => (imageUrl = e.target.value)} />
        <TextInput
            id="email"
            label="E-Mail"
            value={email}
            type='email'
            valid={emailValid}
            validityMessage='Please enter a valid email.'
            on:input={e => (email = e.target.value)} />
        <TextInput
            id="description"
            label="Description"
            controlType='textarea'
            valid={descriptionValid}
            validityMessage='Please enter a valid description.'
            bind:value={description} />
    </form>
    <div slot="footer">
        <Button type='submit' mode='outline' on:click={cancel}>Cancel</Button>
        <Button type="submit" on:click={submitForm} disabled={!formIsValid}>Save</Button>
        {#if id}
            <Button type='button' on:click={deleteMeetup}>Delete</Button>
        {/if}
    </div>
</Modal>
