<script>

    import TablePageComponentV2 from './TablePageComponentV2.svelte';
    let base_url = 'home2';
    let url_slug = "catAPI4";
    let nested_urls = [{adjusted_url:'/first',second_edit:-1},{adjusted_url:'/product',second_edit:4}];
    let started = 0;
    import {title} from '../stores.js'
    $title = "Edit category";
    </script>
<TablePageComponentV2 {url_slug} {base_url} {started} {nested_urls}  />
