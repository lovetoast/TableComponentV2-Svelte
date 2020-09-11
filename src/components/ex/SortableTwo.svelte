<script>
import Sortable from 'sortablejs';



import { beforeUpdate, afterUpdate, onMount } from 'svelte';
export let lists;
export let listNew;
export let prefill;
let nolist ={};
let islist ={};
let lists2 = [];
let root;
let root2;

if (typeof listNew != "undefined") {
if (listNew != null)
lists2 = listNew.split(",");


}

nolist['name'] = [];
nolist['val'] =[];

islist['name'] = [];
islist['val'] =[];

console.log(prefill);
console.log(lists);


onMount(async () => { 
    let i;
    let k;
    let gotThese = [];
    for (i in lists2) {
        for (k in prefill.val) {
            if (lists2[i] == prefill.val[k]) {
                islist['val'].push(prefill.val[k]);
                islist['name'].push(prefill.name[k]);
                gotThese.push(prefill.val[k]);

            }
          
        }
       
    }
    for (i in prefill.val) {
        if (gotThese.includes(prefill.val[i]) === false) {
            nolist['val'].push(prefill.val[i]);
            nolist['name'].push(prefill.name[i]);
        }
    }
    nolist = nolist;
    islist = islist;
    console.log(islist);


    var example2Left = root;
    var example2Right = root2;
    new Sortable(example2Left, {
    group: 'shared', // set both lists to same group
    animation: 150,
    onSort: function (/**Event*/evt) {
        var slides = root.children;
        lists = [];
        for (var i = 0; i < slides.length; i++) {
        let myid  = slides.item(i).getAttribute('data-myid');
        console.log(myid);
         lists.push(myid);
        }
        lists =lists;
	},
});

new Sortable(example2Right, {
    group: 'shared',
    animation: 150
});

})
</script>


<div id="shared-lists" class="row">
    <h4 class="col-12">Shared lists</h4>
    <div  bind:this={root} class="list-group col">
        {#each islist['val'] as list,k}
        <div data-myid={list} class="list-group-item newlist">{islist['name'][k]}</div>
    {/each}
    </div>

    <div  bind:this={root2}  id="example2-right" class="list-group col">
        {#each nolist['val'] as list,k}
        <div data-myid={list} class="list-group-item oldlist">{nolist['name'][k]}</div>
    {/each}
    </div>
</div>