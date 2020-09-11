<script>
import Sortable from 'sortablejs';



import { beforeUpdate, afterUpdate, onMount } from 'svelte';
export let gridData;
export let lists;
let root;

onMount(async () => { 


    var example2Left = root;
    new Sortable(example2Left, {
    animation: 150,
    onSort: function (/**Event*/evt) {
        var slides = root.children;
        lists = [];
        for (var i = 0; i < slides.length; i++) {
            let myid  = slides.item(i).getAttribute('data-myid');
            let newObj = {Id:myid,sorter:i}
            lists.push(newObj);
            
        }
        lists =lists;
	},
    });

    })
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

</script>


<div id="shared-lists" class="row">
    <h4 class="col-12">Shared lists</h4>
    <div  bind:this={root} class="">
        {#each gridData as list,k}
        <div style="" data-myid={list.Id}  data-sorter={list.sorter}  class="newlist">{list.Name}</div>
    {/each}
    </div>

</div>
<button on:click={() => dispatch('update') }>UPDATE</button>
<style>
.newlist {
    width: 100px;
    height: 100px;
    display: inline-block;
    background-color: #fff;
    border: solid 1px rgb(0,0,0,0.2);
    padding: 10px;
    margin: 12px;
    overflow:hidden

}
</style>