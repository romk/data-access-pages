[Home](.)

* TOC
  {:toc}

# Web Service API

The Data Access Facility Web Service API is a RESTful API that allows you to access data in relational databases.

It exposes a set of endpoints that allow you to retrieve table contents and to post data to tables.

## Authentication

This endpoint allows you to authenticate with the Data Access Facility Web Service. You submit database name, username
and password. After successful authentication, the Web Service returns a JWT token that you can use for subsequent
requests.

- Method: `POST`
- Endpoint: `/data_access/1.0/auth`
- Form Data: `database`, `username`, `password`

Returns:

```json
{
  "token": "..."
}
```  

## Tables

This endpoint allows you to retrieve a list of tables in the database, filtered by a search string. The Web Service
returns a list of tables that match the search string, including a rowcount for each table.

- Method: `GET`
- Endpoint: `/data_access/1.0/tables`
- Query Parameters:
    - `q`: the search string to filter tables

Returns:

```json
[
  {
    "name": "...",
    "count": ...
  },
  ...
]
```

## Header

This endpoint allows you to retrieve the mapping for a table. This can be de the default mapping based on the database
schema, or you can submit a custom mapping that the Web Service will verify and enrich with additional information.
The Web Service returns the mapping in either JSON or CSV format.

- Method: `GET`
- Endpoint: `/data_access/1.0/:table_name/header`
- Path Parameters:
    - `table_name`: the table name to retrieve the mapping for
- Query Parameters:
    - `h`: the custom mapping to verify and enrich
    - `style`: the output style, either 'json' (default) or 'csv'

Returns:

- `CSV style`: a CSV file with the mapping, for example:

```
id[unique=true], category_id(id), comment, name
```

- `JSON style`: a JSON object with the mapping, for example:

```json
{
  "table-name": "res_groups",
  "columns": [
    {
      "key": "id[unique=true]",
      "type": "integer",
      "default": true,
      "enabled": true,
      "in-use": true,
      "primary-key": true,
      "unique": true
    },
    ...
    }
```

## Row Count

This endpoint allows you to retrieve the number of rows in a table, based on an additional where clause. Specifying an
additional where clause is not trivial, because it requires a valid SQL WHERE clause that correctly uses the table and
column aliases that the Web Service chooses for its SQL query.
The Web Service returns a JSON object containing a count of the matching rows.

- Method: `GET`
- Endpoint: `/data_access/1.0/:table_name/count`
- Path Parameters:
    - `table_name`: the table name to retrieve the row count for
- Query Parameters:
    - `q`: the where clause to filter rows

Returns:

```json
{
  "count": ...
}
```

## Retrieve Table

This endpoint allows you to retrieve the contents of a table, based on a header and additional where clause.

- Method: `GET`
- Endpoint: `/data_access/1.0/:table_name`
- Path Parameters:
    - `table_name`: the table name to retrieve the contents for
- Query Parameters:
    - `h`: the header mapping to use, in CSV format
    - `q`: the where clause to filter rows

Returns:

```
id[unique=true],auto_install_required[filter=$],module_id(name),"name[filter=""$ = 'account'""]"
17,True,snailmail_account,account
259,True,account_fleet,account
...
```

## Post Table

This endpoint allows you to post data to a table, based on a header and the data to post. You can choose to either
provide the mapping on the first line of the body data, or to use the header parameter. The header parameter has
priority over a header in the body data.
If you specify a header in the body data, then make sure to also specify skiprows=1, so that the Web Service knows to
skip the header row.

This operation requires that you specify at least one unique header column, so that the Web Service can identify the
rows to insert, update or delete.

You can choose which of the insert, update and delete operations to perform, and whether to execute and commit the
transaction.

All operations are idempotent, meaning that you can repeat the same operation multiple times without changing the
resulting data in the table.

The Web Service interprets rows that exist in the target table but not in the posted data as rows to delete. It
interprets rows that exist in the posted data but not in the target table as rows to insert. It interprets rows that
exist in both the target table and the posted data as rows to update.

By default, the Web Service takes into account all rows in the target table and issue delete statements for all rows
that do not exist in the posted data. You can change this behavior in the following ways:

- by specifying a where clause that filters the rows in the target table
- by specifying a 'filter' modifier in the header mapping that filters the rows in the target table
- by specifying 'delete=false' in the query parameters, so that the Web Service does not issue delete statements

The Web Service returns SQL statements and parameter that it would execute, in CSV format. If you specify `execute=true`,
then the Web Service also returns the number of rows effected by each SQL statement.

- Method: `POST`
- Endpoint: `/data_access/1.0/:table_name`
- Path Parameters:
    - `table_name`: the table name to post data to
- Query Parameters (all boolean parameters have default value 'False'):
    - `h`: the header mapping to use, in CSV format
    - `q`: the where clause to filter rows
    - `skiprows`: the number of rows to skip before posting data
    - `insert`: whether to insert new rows
    - `update`: whether to update existing rows
    - `delete`: whether to delete rows
    - `execute`: whether to execute the transaction
    - `commit`: whether to commit the transaction
    - `style`: the output style, must be 'sql'

Returns:

```
rows,sql,id
1,delete from ir_module_module_dependency where ir_module_module_dependency.id = :id,973
```