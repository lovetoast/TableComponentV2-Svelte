<script>
    import '../polyfill'
    import { beforeUpdate, afterUpdate, onMount } from 'svelte';
    import SortableTwo from '../ex/SortableTwo.svelte'

    export let formFields;
    export let formData;
    export let formName;
    export let actionCol_edit;
    export let pre_fill_edit;
    export let tableHeaderTypes_edit;
    export let base_url;
    export let adjusted_url = "";
    export let nested_urls;
    export let url_slug;
    export let started;
    export let query_var_url;
    let showNested = false;

    
    let query_var = 0;
    let base_url2 = '';
    let actionCol = actionCol_edit;
    let pre_fill = pre_fill_edit;
    let tableHeaderTypes = tableHeaderTypes_edit;
    import TablePageComponentV2 from '../TablePageComponentV2.svelte';
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();
    const returnTable = () => dispatch('returnTable');
  

    onMount(async () => {
      adjusted_url = nested_urls[started].adjusted_url;
      console.log(started + "istared")
      if (typeof nested_urls[started+1] != "undefined" && formName=="Edit") {
        let myId = nested_urls[started+1].second_edit;
        query_var = formData[myId];
        showNested = true;
      }

    
    })
$: console.log(formData);

    const submit = async returnTo => {
      if (typeof nested_urls[started+1] != "undefined") {
        if (query_var !=formData[nested_urls[started+1].second_edit]) {
          showNested = false;
        }
      }

      dispatch('showLoader')
      let response = await fetch(`${base_url}/${url_slug}/edit${adjusted_url}/${query_var_url}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          body: JSON.stringify(formData)
      }); 
      try {
      if (returnTo != 1) {
        let jsonInitial = (await response.json());
        formData =  Object.values(jsonInitial.row_data);
        formName = "Edit";
        let myId = nested_urls[started+1].second_edit;
        query_var = formData[myId];
        showNested = true;
      } else {
        //dispatch('showLoader')
        dispatch('returnTable');
        return false;
      }

      } catch (e) {

      }
      dispatch('showLoader')
    };

    async function deletePage() {
      dispatch('showLoader')
      const initial = await fetch(`${base_url}/${url_slug}/get${adjusted_url}/${formData[actionCol]}/delete`);
      returnTable();
    }

 
    </script>
    
    <div class="row">
        <div class="container">
          <form>
            <h2>{formName}</h2>
            <div class="row">
            {#each formFields as formField,i}
              <div class="col-md-6"  hidden={i == actionCol || tableHeaderTypes[i] == "h"} >
                <div class="form-group">
                  <label for="last">{formField}</label>
                  {#if tableHeaderTypes[i] == "d"}
                  <input bind:value={formData[i]} type=date class="form-control" placeholder="" id="">
                  {:else if tableHeaderTypes[i] == "s" && typeof pre_fill[i] != "undefined"}
                  <select bind:value={formData[i]} class="form-control">
                    {#each pre_fill[i]['val'] as fill,k}
                    <option value="{fill}">{pre_fill[i]['name'][k]}</option>
                    {/each}
                  </select>
                    {:else if tableHeaderTypes[i] == "m" && typeof pre_fill[i] != "undefined"}
                    <SortableTwo prefill={pre_fill[i]} listNew={formData[i]} bind:lists={formData[i]} /> 
                    {:else}
                  <input bind:value={formData[i]} type=text class="form-control" placeholder="" id="">
                  {/if}
                </div>
              </div>
              {/each}
              <!--  col-md-6   -->
            </div>
        
            <div class="row">
        
        
            <button class="btn btn-primary" on:click|preventDefault={() => submit(0)}>Submit</button>
            <button class="btn btn-primary" on:click|preventDefault={() => submit(1)}>SubmitReturn</button>
            <button class="btn btn-primary" on:click|preventDefault={returnTable}>RETURN</button>
            {#if formData[actionCol] > 0}
            <button class="btn btn-primary" on:click|preventDefault={deletePage}>DELETE</button>
            {/if}
  
    
          </form>
        </div>
    
    
      </div>
      {#if (showNested == true)}
      <TablePageComponentV2 {base_url} {nested_urls} {query_var} started={started+1} {url_slug} />
      {/if}
