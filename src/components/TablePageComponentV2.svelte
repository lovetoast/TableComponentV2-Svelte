<script>

  let dataLine = {};
  import './polyfill'
  import { beforeUpdate, afterUpdate, onMount } from 'svelte';
  import TablePageEditV2 from './forms/TablePageEditV2.svelte';
  import SDateRangePicker from "s-date-range-picker";
  import Line from "svelte-chartjs/src/Line.svelte"
  import {title} from '../stores.js'
  import {getPageList2,getPageList} from './orders'
  export let base_url = 'https://chi2.f4food.net/home2'
  export let second_edit = -1;
  export let adjusted_url;
  export let nested_urls =[];
  export let started = 0;
  export let query_var = '';
  let action_url = nested_urls[started].adjusted_url;
  console.log(started);
  console.log(action_url);
  let tickets = [];
  export let url_slug;
  let run = false;
  let perPage = 10;
  let per = perPage;
  let currentPage = 1;
  let searchBy = '';
  let searchByBox = searchBy;
  let searchOn = 0;
  let searchOnAl = searchOn;
  let sortBy = 0;
  let by = 'ASC'
  let totalPages = 0;
  let totalResults = 0;
  let totalData = [];
  let bodyData = [];
  let allowPer = [10,25,50,100,"all"];
  let tableHeaders = [];
  let tableHeaderTypes = [];
  let tableHeaderTypes_edit = [];
  let loading = false;
  let startFetch = false;
  let actionCol = 0;
  let actionCol_edit = 0;
  let formFields;
  let formName = "Edit order";
  let formData = {};
  let editMode = false;
  let showDate = false;
  let pre_fill = [];
  let pre_fill_edit = [];
  let newData = [];
  let newDataLength = 0;
  let pageArray = [];
  
  onMount(async () => {
    run = true;
  })
  $: {
    if (tableHeaderTypes[searchOn] == 'd') {
      showDate=true;
    } else {
      showDate=false;
    }
  }
  $: {
    if (run) { 
     startData(perPage,currentPage,searchBy,searchOn,sortBy,by);
    }
  }

  async function getEdit(id = '-1',action='edit') {
    loading = true;
    if (action != 'delete')  {
      if (action == "edit") {
      const initial = await fetch(`${base_url}/${url_slug}/get${action_url}/${id}`);
      let jsonInitial = await initial.json();
      tableHeaderTypes_edit =jsonInitial.data_types;
      actionCol_edit =jsonInitial.edit_col;
      pre_fill_edit =jsonInitial.pre_fill;
      if (id != '-1') {
        formData =  Object.values(jsonInitial.row_data);
        formFields =jsonInitial.col_headers;
        formFields = formFields.split(",");
        formName = 'Edit';
        editMode = true;
        
      } else {
        formFields =jsonInitial.col_headers;
        formName = 'Insert';
        formFields = formFields.split(",")
        formData = new Array(formFields.length)
        editMode = true;
      }
    } else if (action == "edit2") {
      let response = await fetch(`${base_url}/${url_slug}/inline${action_url}/${query_var}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          body: JSON.stringify(id)
      }); 
      await startData(perPage,currentPage,searchBy,searchOn,sortBy,by);
    } else {
      let k;
      //for (k in id) {
        
      let response = await fetch(`${base_url}/${url_slug}/inline${action_url}/${query_var}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          body: JSON.stringify(id)
      }); 
      await startData(perPage,currentPage,searchBy,searchOn,sortBy,by);
    }
    } else {
      const initial = await fetch(`${base_url}/${url_slug}/get${action_url}/${id}/delete`);
      await startData(perPage,currentPage,searchBy,searchOn,sortBy,by);
    }
    loading = false;
  }
  let globDate;
  async function startData(perPage,currentPage,searchBy1,searchOn,sortBy,by,byDate=false) {
    if (perPage == "all") {
      currentPage = 1;
    }
    loading = true;
    let initial; 
    let checkType = typeof tableHeaderTypes[searchOn] == "undefined" ? "a" : tableHeaderTypes[searchOn];
    if (tableHeaderTypes[searchOn] != 'd' ) {
      if (searchBy1 == "")
        searchBy1 = " ";
      searchBy1 = encodeURI(searchBy1);
      initial = await fetch(`${base_url}/${url_slug}/search${action_url}/${currentPage}/${perPage}/${searchBy1}/${searchOn}/${sortBy}/${by}/${query_var}`);
    }
    else {
      searchBy1 = encodeURI(" ");
      initial = await fetch(`${base_url}/${url_slug}/search${action_url}/${currentPage}/${perPage}/${searchBy1}/${searchOn}/${sortBy}/${by}/${query_var}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          body: JSON.stringify(globDate)
      });
    }
    let jsonInitial = await initial.json();
    totalPages = jsonInitial.total_pages;
    let initialBodyData = jsonInitial.main_data;
    tableHeaderTypes =jsonInitial.data_types;
    let newBody = [];
    let totalData1 = jsonInitial.sum_data;
    pre_fill =jsonInitial.pre_fill;
    totalData = Object.values(totalData1);
    actionCol = jsonInitial.edit_col;
    let k;
    for (k in initialBodyData) {
      newBody.push(Object.values(initialBodyData[k]));
    }
    totalData = Object.values(totalData1);
    pageArray = getPageList(currentPage,totalPages);

    tableHeaders = jsonInitial.col_headers;
    newDataLength = tableHeaders.length;
    newData = new Array(newDataLength).fill().map((d,i)=>"");


    totalResults = jsonInitial.sum_data.count;
    bodyData = newBody;
    loading = false;
    let theFood = [];
    let howMuchFood = [];
    for (k in initialBodyData) {
      theFood.push(initialBodyData[k].MenuItemName);
      howMuchFood.push(parseFloat(initialBodyData[k].Price));
    }
    theFood =theFood;
    howMuchFood = howMuchFood;
     dataLine = {
  labels: theFood,
  datasets: [
    {
      label:'Sales',
      data: howMuchFood,
    }
  ]
};
    console.log(howMuchFood);
    console.log(theFood)
  }
  async function refreshData() {
    loading =true;
    editMode =false;
    await startData(perPage,currentPage,searchBy,searchOn,sortBy,by);
    loading = false;
  }
  function setSort(i) {
    if (sortBy == i) {
      by = by == 'ASC' ? 'DESC' : 'ASC';
    }
    sortBy = i;

  }
  function setPage(i) {
    currentPage = i;
  }
  /*function setPer() {
    perPage = per;
    currentPage = 1;
  }*/
  function setSearchOn() {
    searchBy = searchByBox;
    searchOn = searchOnAl;
    currentPage = 1;
  }
  function resetSearch() {
    searchBy = 0;
    by = 'ASC';
    sortBy = 0;
    searchOn = searchOnAl = 0;
    searchByBox = searchBy = "";
    per = perPage = allowPer[0];
    currentPage = 1;

  }

  // Manage start and end props from main app component
  let startDate = new Date();
  let endDate = new Date();

  // Update state on apply event
  function onApply({ detail }) {
    startDate = detail.startDate;
    endDate = detail.endDate;
    globDate = [startDate,endDate];
    startData(perPage,currentPage,[startDate,endDate],searchOn,sortBy,by,true);
  }
</script>
<div hidden={!loading} style="position: absolute;
top: 413px;
left: 624px;
height: 10rem;
width: 10rem;" class="spinner-border" role="status">
  <span class="sr-only">Loading...</span>
</div>
<span hidden={editMode}>
<div class="row"  style="{loading ? 'opacity: 0.2;' : ''}">

<div class="row">
  <div class="col-md-2">
    <!-- svelte-ignore a11y-no-onchange -->
    <select bind:value={perPage} on:change={() => currentPage = 1}>
    {#each allowPer as pers,i}
    <option value="{pers}">{pers}</option>
    {/each}
  </select>
  </div>
  <div class="col-md-2">
    <!-- svelte-ignore a11y-no-onchange -->
    <select bind:value={searchOnAl} on:change={setSearchOn}>
      {#each tableHeaders as headers,i}
      <option value="{i}">{headers}</option>
      {/each}
  </div>
  <div class="col-md-6"><input hidden={showDate}  bind:value={searchByBox} on:blur={setSearchOn}>  
   <span  style="margin-left: 146px;" hidden={!showDate}><SDateRangePicker   {startDate} {endDate} on:apply={onApply} /></span>
  </div>
  <!--<div class="col-md-1"><button on:click={setSearchOn}>Search</button></div>-->
  <div class="col-md-1"><button on:click={resetSearch}>Reset</button></div>
  <div class="col-md-1"><button on:click={() => getEdit('-1')}>Add</button></div>
</div>
</div>


{#if bodyData.length > 0}
<div class="row" style="{loading ? 'opacity: 0.2;' : ''}">
  <a style="margin-top:50px;display:block" href="" >TAKE ME HOME</a>
{perPage != "all" ? 'Showing from ' + ((parseFloat(currentPage) -1) * parseFloat(perPage) + 1) + ' to ' + currentPage * perPage : ''}
<table class="table" >
  <thead>
    <tr>
      {#each tableHeaders as headers,i}
      {#if tableHeaderTypes[i] != "h"}
      <th scope="col"  class="{sortBy == i ? by == 'ASC' ? 'selectSortASC' : 'selectSortDESC' : ''}"  on:click={() => setSort(i)} >{ i== actionCol ? 'Actions' : headers} {@html sortBy == i ? by == 'ASC' ? '<i class="fa fa-arrow-circle-up"></i>' : '<i class="fa fa-arrow-circle-down"></i>' : ''}</th>
      {/if}
      {/each}
    </tr>
  </thead>
  <tbody>
    {#each bodyData as bodies,k}
    <tr>
      {#each bodies as body,i}
      {#if tableHeaderTypes[i] != "h"}
      {#if i == actionCol}
      <th scope="row" ><i on:click={getEdit(body)} class="fa fa-pencil"></i><i on:click={getEdit(body,'delete')} class="fa fa-minus-circle"></i><i on:click={getEdit(bodies,'edit2')} class="fa fa-pencil"></i></th>
      {:else if tableHeaderTypes[i] == "s" && typeof pre_fill[i] != "undefined"}
      <th scope="row">
      <select bind:value={body}  class="form-control">
        {#each pre_fill[i]['val'] as fill,k}
        <option value="{fill}">{pre_fill[i]['name'][k]}</option>
        {/each}
      </select>
    </th>
      {:else if isNaN(body) || i == 0 || body == "" || tableHeaderTypes[i] == 'd'}
      <th scope="row">{body}</th>
      {:else}
      <th scope="row">{parseFloat(body).toFixed(2)}</th>
      {/if}
      {/if}
      {/each}
    </tr>
    {/each}
    <tr>
      {#each newData as new1,i}
      {#if tableHeaderTypes[i] != "h"}
      {#if i == actionCol}
      <th scope="row" ><i on:click={getEdit(newData,'quickadd')} class="fa fa-plus-circle"></i></th>
      {:else if tableHeaderTypes[i] == "s" && typeof pre_fill[i] != "undefined"}
      <th scope="row">
      <select bind:value={newData[i]}  class="form-control">
        <option value="">Select</option>
        {#each pre_fill[i]['val'] as fill,k}
        <option value="{fill}">{pre_fill[i]['name'][k]}</option>
        {/each}
      </select>
    </th>
      {:else if tableHeaderTypes[i] == "d"}
      <th scope="row"><input type="date" style="width:100%" bind:value={new1}></th>
      {:else}
      <th scope="row"><input style="width:100%" bind:value={new1}></th>
      {/if}
      {/if}
      {/each}
    </tr>
  </tbody>
  <tfoot>
    <tr>
    {#each totalData as bodies,k}
    {#if k == 0}
      <th scope="row">#{bodies}</th>
    {:else if (!isNaN(bodyData[0][k]) && bodyData[0][k] != "" && k != actionCol && tableHeaderTypes[k] != "s")}
      <th scope="row">{parseFloat(bodies).toFixed(2)}</th>
    {:else}
      <th scope="row"></th>
    {/if}
    {/each}
  </tr>
  </tfoot>
</table>
<nav aria-label="Page navigation example" style="{loading ? 'opacity: 0.2;' : ''}">
  <ul class="pagination" style="flex-wrap:wrap">
    {#if totalPages < 11}
    {#if currentPage != 1}
    <li class="page-item"><a class="page-link" on:click={() => setPage(currentPage-1)}>Previous</a></li>
    {/if}
    <!-- svelte-ignore a11y-missing-attribute -->
    {#each Array(totalPages) as _, i}
    <li class="page-item {currentPage-1 == i ? 'selectedPage': ''}"><a class="page-link" on:click={() => setPage(i+1)}>{i+1}</a></li>
    {/each}
    {#if currentPage != totalPages && totalPages != 0}
    <li class="page-item"><a class="page-link" on:click={() => setPage(currentPage+1)}>Next</a></li>
    {/if}
    {:else}
    
    {#if currentPage != 1}
    <li class="page-item"><a class="page-link" on:click={() => setPage(currentPage-1)}>Previous</a></li>
    {/if}
    <!-- svelte-ignore a11y-missing-attribute -->
    {#each pageArray as pageIt,i}
    {#if pageIt != 0}
    <li class="page-item {currentPage == pageIt ? 'selectedPage': ''}"><a class="page-link" on:click={() => setPage(pageIt)}>{pageIt}</a></li>
    {:else}
    <li class="page-item"><a class="page-link">...</a></li>
    {/if}
    {/each}
    {#if currentPage != totalPages && totalPages != 0}
    <!-- svelte-ignore a11y-missing-attribute -->
    <li class="page-item"><a class="page-link" on:click={() => setPage(currentPage+1)}>Next</a></li>
    {/if}
    {/if}
  </ul>
</nav>
</div>
{:else} 
EPIC FAIL
{/if}
</span>
{#if editMode}
<div style="{loading ? 'opacity: 0.2;' : ''}">
<TablePageEditV2 {url_slug}  query_var_url={query_var} {nested_urls} {started} {base_url} {second_edit} {adjusted_url} {pre_fill_edit} {tableHeaderTypes_edit} {actionCol_edit} {formFields} {formData} {formName} on:returnTable={refreshData} on:showLoader={() => loading = !loading}></TablePageEditV2>
</div>
{/if}
{#if !editMode}
{#if bodyData.length > 0}
<div style="{loading ? 'opacity: 0.2;' : ''}">
<Line data={dataLine} options={{ responsive: true }}/>
</div>
{/if}
{/if}
<style>
  :global(.s-date-range-picker) {
    width:100% !important;
  }
  .selectedPage {
    font-weight:900;
  }
  .selectSortASC {
    color:red;
  }
  .selectSortDESC {
    color:green;
  }
</style>
