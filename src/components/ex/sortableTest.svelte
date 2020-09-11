<script>
import SortableTwo from './SortableTwo.svelte'
let lists = [7,8];
let lists2 = JSON.parse(JSON.stringify(lists))
let prefill = {};

prefill['val'] = [7,8,9,10];
prefill['name'] = ['cow','dog','bull','lion']

$: console.log(lists);

</script>
<SortableTwo {prefill} listNew={lists2} bind:lists />