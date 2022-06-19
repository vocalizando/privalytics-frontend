<script>
    import {deleteEntry} from "./privalytics-js.js";

    export let entry;
    export let user_data;
    export let hostname;
    let entry_date = new Date(entry.metadata.date);
    let show = true;
    let errored = false;

    async function deleteEntryHandler() {
        try {
            await deleteEntry(hostname, user_data, entry.metadata.duid)
        } catch(e) {
            errored = true
        }
        show = false
    }
</script>

<li class="list-group-item">
    {#if show}
        <b>Metadata:</b><br>
        Date: {entry_date.getDay()}/{entry_date.getMonth()}/{entry_date.getFullYear()}<br>
        DUID: {entry.metadata.duid}<br>
        {#if entry.metadata.page}
            Page: {encodeURI(entry.metadata.page)}<br>
        {/if}
        {#if entry.metadata.uid}
            Page: {encodeURI(entry.metadata.uid)}<br>
        {/if}

        <br><b>Data</b><br>
        {#each Object.keys(entry.data) as key (key)}
            <i>{key}</i> &rightarrow; {entry.data[key]}
        {/each}<br><br>

        <button on:click|once|trusted|preventDefault={() => deleteEntryHandler()}>Delete entry</button><br><br>
    {:else}
        {#if errored}
            <i>Failed to delete</i>
        {:else}
            <i>Deleted</i>
        {/if}
    {/if}
</li>

<style>
    button {
        background-color: #dc3545;
        border: none;
        color: white;
        padding: 10px 12px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        border-radius: 10px;
        font-size: 15px;
    }

    button:hover {
        cursor: pointer;
    }
</style>
