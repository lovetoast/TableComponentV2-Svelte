# API guide for TablePageComponentV2

This component was tested using a PHP API on the CodeIgniter framework. It can be extended to any programming language/framework.

4 different end-points are required:

1) **search** to get the data for the table
2) **get** for getting the fields for the form, as you may have different information to add/edit, compared to the main table. This also handlees deleting.
3) **edit** for adding/editing from the main form
4) **inline** for in-line editing and adding

The component can be edited for a more standard API quite easily. If you do this, create a branch with a PR, and test it, prior to pushing new code with your new component.

![](table.gif)
 

## Search

### Request (via URL segments):

> In the following examples, the segment of the URL (2nd part in this case) that says **first** is just to denote that this is the first part of the nested function, so that the server knows this. It doesn't mean you're looking at the first record or anything to that effect. It is purely to identity which operation we're looking at.

```
search/first/1/10/%20/0/2/DESC/
```
From segment 3, we have:

1) Page number
2) Items per page
3) Search query
4) Field to search on
5) Column to sort on
6) Sort order

### Response

![](uploads/2020-09-10-21-31-57.png)

In turn:

1) Column headings
2) Column headings - not used yet
3) Date types - i.e. is this a date, dropdown etc
4) Data types, not used yet
5) Which column is the primary key
6) Array of objects of data
7) Data for dropdowns
8) ignore
9) Sums/counts
10) Total pages

If, for example, you pass data that requires a dropdown, and this was the second element, your datatype array would have the 2nd element as **s** and in **pre_fill**, in the second element of the array, give an array of objects with 2 keys:

1) val
2) name <- This is what is actually displayed

> **IMPORTANT** You will notice in the API that for the **search**, **edit** and **inline** endpoints, a parameter is expected for the nested tables, as follows:

```

edit/product/16307

```
This tells the server, that for a nested table, only search/return records that match 16307 on the related field in the nested table.
<br>

## Get

### Request

```
get/first/16307
```

Get the ID **16307** from the database and return the data. If **-1** is supplied instead, the server will know a new record is being created.

### Response

A form is rendered, based on the following data:

![](uploads/2020-09-10-21-43-25.png)

This is quite similar to before. Ignore the keys ending in **2**, they were used in my API so I could declare a group of variables towards the top of the page. The main thing here is the data is returned in **row_data** if it is an existing record, otherwise, the main purpose of this function is to get a list of fields for the form.

> Remember, the reason for this is sometimes, your form may contain more [or less, though less likely] fields to edit.

## Edit

### Request

An array is expected:

["veg food","hey","16307"]: 

The last element here is the primary key, if it is empty, my API makes a new record, and the first two are data to be inserted. I use _REPLACE_ in Codeigniter.


### Response

![](uploads/2020-09-10-21-52-39.png)

Again, the main thing here is the **row_data** that returns the information that was inserted.

### Inline

This is a shortened edit/add function that can be used for operations performed _without_ going to the form. Obviously, you can only edit items that are being shown on the table, for anything else, the user would use the full form.

The parameters and response are similar to the edit form, however, we do not need to return anything as upon editing/adding, the form will automatically be refreshed to reflect the new data added.

>As of 10/09/2020, client-side validation, multi-select support, datetime support missing.



