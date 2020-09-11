
<script>
let db;
let collapsed1 = true;
import router from "page"
let page;
let params;
import MenuItemsPage from './components/MenuItemsPage.svelte'  
let displayState = '';
router('/MenuItemsPage', () => page = MenuItemsPage);

router.start();


</script>

<svelte:head>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">

  <link href="  https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css  " rel="stylesheet" type="text/css">
  <link href="https://fonts.googleapis.com/css?family=Nunito:200,200i,300,300i,400,400i,600,600i,700,700i,800,800i,900,900i" rel="stylesheet">

		<script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap.native/3.0.0/bootstrap-native.min.js"></script>


</svelte:head>

<body id="page-top" class="sidebar-toggled">
<div>
        
    <svelte:component this={page}   />

</div>
     

</body>

