<script>
    import SortableGrid from './SortableGrid.svelte'
    let dataLoad = initialDataLoad();
    let gridData;
    let lists;

    async function initialDataLoad(run=false) {
        if (run) {
            let response = await fetch(`updateSort`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          body: JSON.stringify(lists)
      }); 
        }
        const tLayoutJson = await fetch(`getKitchen`);
        let tLayoutJSON2 = await tLayoutJson.json();
        gridData = tLayoutJSON2['menu'];
        lists = gridData;
        return true;

    }
    async function update() {   
      dataLoad = initialDataLoad(true);
    }
    
    </script>
    {#await dataLoad}
    <div style="position: absolute;
        top: 250px;
        left: 200px;
        height: 10rem;
        width: 10rem;" class="spinner-border" role="status">
    <span class="sr-only">Loading...</span>
    </div>
    {:then}
    <SortableGrid {gridData}  bind:lists on:update={update} />
    {/await}