# Google Sheets Client

The Google Sheets Client is a simple but powerful tool that allows you to access the Data Access Facility using Google
Sheets. The app is designed with harmonica-style sections that open one at a time to guide you through interaction with
the Data Access Facility.

## Installation

The Data Access app is not yet available on the Google Workspace Marketplace. You can install it into your sheet from
source by following these steps:

1. **Open Google Sheet**: Open an existing Google Sheet or create a new one on https://docs.google.com/spreadsheets
2. **Copy Project ID**: Open the script editor by clicking on `Extensions > Apps Script` and copy the project ID from
   the settings page
2. **Source Code**: Clone the latest source code from https://github.com/romk/daa-gas into a folder of your choice
3. **Install clasp**: Install the [clasp](https://developers.google.com/apps-script/guides/clasp) command line tool
4. **Login to Google**: Run `clasp login` and follow the instructions to login to your Google account. This saves your
   credentials to `~/.clasprc.json`
5. **Clone the Project**: Run `clasp clone <project-id>` to clone the project into the current folder
6. **Reset the Workspace** Run 'git reset --hard' to reset the workspace to the latest version
7. **Push the Code**: Run `clasp push` to push the code to your Google Sheet
8. **Run the Code**: Run the code by clicking on `Run` in the script editor. After granting permission, this will create
   a new menu item in your Google Sheet called `Data Access`

## Connection

The Data Access application is running in Google's cloud. You can use it to connect to a Data Access Facility webservice
installed on odoo.sh by following the steps below. It is also possible to run the Data Access Facility on your own
server,
but then you need to configure your firewall and router so that it is accessible from the Internet.

1. **Open Connection tab**: Open the Google Sheet and click on the `Data Access` menu item, then click
   on `Instant Data Access`
2. **Enter Database name**: Enter the name of the Odoo database you want to connect to. For non-production builds, you
   can find the database name in the URL of your odoo.sh instance, it's the part before 'dev.odoo.com'. For all builds,
   you can find the database name at the top right when in debug mode.
3. **Enter Username and Password**: Enter the username and password of the Odoo user you want to connect with

The app submits username and password to the Data Access Facility webservice. The webservice returns a token that the
app then
uses for subsequent requests. The app does not store the password.

If authentication is successful, the app will open the Tables section to show a list of tables in the database.

## Tables

Once connected, the Headers section lists all non-empty tables in the database. You can find and open a table by
following these
steps:

1. **Filter by table name**: Enter a table name in the search box to filter the list of tables. You can enter a partial
   name
   to find tables that contain the text you entered.
2. **Include empty tables**: Check the box to include empty tables in the list. Empty tables are not shown by default.
3. **Open table**: Click on the table name to open the table.

The app will now create a new tab in the Google Sheet with the name of the selected table, or open an existing tab if it
already exists. It will then open the Headers section to show the column names of the table.

## Headers

The Headers section lists all columns in the selected table. For each column it shows the following properties:

1. **Selected**: Check the box to include the column in the output
2. **Name**: The name of the column, including nested fields and modifiers
3. **Properties**: Checkboxes to show whether the column is a primary key, unique, a foreign key, in use and/or
   customized

By default, a column is selected if:

- It is a primary key and no other column is identified as unique, or
- It is in use, meaning that at least one row has a value for this column, or
- The table is empty, in which case all columns are selected

You can change the selection by checking or unchecking the box next to the column name. You can drag and drop columns to
change the order in which they appear in the output.

You can use the following buttons at the bottom of the section:

- **Default**: Reset the selection to the default values
- **Apply**: Apply the current selection to the current sheet

## Filter

The Filter section allows you to specify an SQL WHERE clause to filter the rows in the table. This section has the
following components:

- **Enable Filter**: Check the box to enable the filter
- **Where Clause**: Enter an SQL WHERE clause to filter the rows in the table
- **Test**: Click on the Test button to test the filter. The app will show the number of rows that match the filter
- **Apply**: Click on the Apply button to apply the filter to the table

## Actions

The Actions section allows you to extract data from the table and to post data to the table. The app performs these
operations on the current sheet, treating the sheet name as table name.

This section has the following components:

- **Extract Table**: Click on the Extract button to extract the data from the table and display it in the current Google
  Sheet. If the current sheet contains a header row, then the app will use that to map the columns to the data. If no header row is available, then the app will use a default mapping.
- **Insert / Update / Delete**: Check these box to include insert / update / delete operations when posting to a table
- **Execute**: Check this box to execute the insert / update / delete operations when posting to a table. If this box is not checked, then the app will only show the SQL statements that would be executed.
- **Commit**: Check this box to commit the insert / update / delete operations when posting to a table. 
- **Post Table**: Click on the Post button to post the data from the current Google Sheet to the table. The app will use the header row to map the columns to the data.
- **View Result**: Click on the View button to view the result of the last operation, this opens the sheet named 'result'
- **Hide Result**: Click on the Hide button to hide the result of the last operation, this hides the sheet named 'result'










