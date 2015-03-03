Join Tables in Excel
===========================================

Summary:  You can join two tables of data and assign values for a single parameter by referencing that same parameter’s values in another set of data.  In the examples below:

1. The column of data in excel that is common between the two data sets and is referenced to pull in values is referred to as the “reference column”.
1. The Excel sheet which contains the data that is being merged (we are creating new values for an existing parameter) is referred to as the “merged sheet”.
1. The Excel sheet which contains the data and values we want to reference and pull into the merged sheet is referred to as the “reference sheet”.

## Step 1: Export

- Find the common parameter in both lists that you will use as a reference.  There must be a common parameter to reference to join 2 sets of data.
- Fields labeled as “id” are actually the same as “{name of data set}_ID” in other exports.  So, for instance, the “id” field in the “Links” export is the same field as the “link_id” in the all other exports, and the “id” field in the “Links Clicks” export is the same as the “link_clicks_id” field all other exports, etc.

![Export Your Data From Dashboard](https://s3-us-west-1.amazonaws.com/branch-guides/export_dashboard.png)

## Step 2: Open CSV into Excel

- After you export data from the Branch.io Dashboard, you’ll receive an email with a link to download the exported data.  Click on that link and your csv file will download.
- The data should now be organized into the appropriate columns with headers.  Do the same with your 2nd export on a separate sheet.  Label each sheet in your workbook appropriately.

## Step 3: Format the Merged and Reference Sheets for Vlookup

- Take the header names of the data fields from your reference sheet you wish to add to your merged sheet, and add them as the header to new columns in your merged sheet.
- For each column, you are going to perform a “vlookup” for that specified header. In the below example we are taking the values for “Channel” and “Data” and referencing the “id” field.

![Excel Headers](https://s3-us-west-1.amazonaws.com/branch-guides/excel_headers.png)
*Merged sheet with columns for values we are going to pull-in, “channel” and “data”, inserted.*

- In you reference sheet, you want to make sure the reference column is to the left of the data fields you want to pull into your merged sheet.  If it is not already, cut and paste it.

![Excel Highlight](https://s3-us-west-1.amazonaws.com/branch-guides/excel_headers.png)
*Reference sheet with reference column “id”, column B, to the left of values “channel” and “data, columns “G” and “M” respectively, which we will be pulling into the merged sheet*

## Step 4: Vlookup

- Click into the first cell of the first column into which you are pulling the new data on your merged sheet.  
- type in “=Vlookup”.  You should get a pop up for the Vlookup function (SS).  Click on it and a formula tab will appear directly beneath the cell. 

![Vlookup 1](https://s3-us-west-1.amazonaws.com/branch-guides/vlookup1.png)
*(Vlookup equation entered and clicked for the first cell of merged data column)*

- Excel’s VLOOKUP function uses 4 pieces of information. The function panel may seem intimidating with the terms, but it’s simpler than it looks. We’ll walk through them here as we are completing the formula. **Just make sure you separate each field by a comma**.
- “lookup_value” - Think of this field as your starting point. In my example, I’ll click cell C2 of my merged sheet so the value is filled in the dialog. I’m requesting Excel take the value of C2 of my merged sheet, which displays the “link_id”, and I’m going to use it to find the “Channel” value that corresponds with it on my reference sheet. (Notice how my data automatically populates in the formula bar above the data in the screenshot below).

![Vlookup 2](https://s3-us-west-1.amazonaws.com/branch-guides/vlookup2.png)

- “Table_array” - This is the range of columns you will highlight in your reference sheet that (left to right) starts with the reference column and includes the columns of values you are looking to pull in to the merged sheet.

![Vlookup 3](https://s3-us-west-1.amazonaws.com/branch-guides/vlookup3.png)
*Columns B through M of my reference sheet (shown in the formula as “Links!B:M”) selected for my “Table_Array”, as I want to include the “Channel” and “Data” columns as values to be pulled in from my reference sheet.*

- “Col_index_num” - On your reference sheet, this is the number in sequence of the column of data you want to pull into your merged sheet in the range that is your “Table_array”.  So, for example, if column A is your reference column and column C contains the values you want to pull in, then the “col_index_num” would be “3”.  In the above example, we want to pull in values for column “G” referencing column “B”, so my col_index_num is “6”.
- Hit enter.  You should see a return for the value you want to merge.  If you see an “#N/A” that means that the specific value you are referencing in that row is not contained anywhere in the reference column in the reference sheet.  You can replace this with a blank “” or “0” by nesting your vlookup formula in an “IFERROR” statement.
- Drag the formula all the way down for all values you want to fill-in. 


