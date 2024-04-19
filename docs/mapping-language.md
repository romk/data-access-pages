[Home](.)

* TOC
  {:toc}

# Mapping Language

The Mapping Language is a simple but powerful language that allows you to define how your relational data model is
mapped to files.

An example mapping for a table that holds users is shown below:

```login[unique=true], name, active[filter=$], company_id(name)[filter="$ like 'company%'", create_date```

This mapping defines the following columns:
- `login`: Mapping the 'login' column in the relational model. The modifier defines it as unique key, so we can use it address rows when posting modification to the table.
- `name`: Mapping the 'name' column in the relational model.
- `active`: Mapping the 'active' column in the relational model. The modifier defines a filter to include only those rows where the 'active' column contains the boolean value 'True'.
- `company_id`: Mapping the 'company_id' column in the relational model, using the 'name' attribute from the referenced record. The modifier defines a filter to include only those rows where the 'company_id' column contains a value that starts with 'company'.
- `create_date`: Mapping the 'create_date' column in the relational model.

## Syntax

The Mapping Language is defined by the following grammar:

```
<columns> ::= <column> | <column> "," <columns>

<column> ::= <attributes> | <attributes> "[" <modifiers> "]"

<attributes> ::= <attributes> | <attribute> ":" <attributes>

<attribute> ::= <identifier> | <identifier> "(" <attributes> ")"

<identifier> ::= [a-zA-Z_][a-zA-Z0-9_]*

<modifiers> ::= <modifier> | <modifier> ":" <modifiers>

<modifier> ::= <identifier> "=" <value> | <identifier> "=" <quoted-value>

<value> ::= [a-zA-Z0-9_$]+

<quoted-value> ::= '"' [^"]* '"'
```

### Columns

A mapping consists of single line of text, listing columns separated by columns. A mapping is a valid CSV format, so it
can be used as a header row in a CSV file.

### Column

A column is defined by a list of attributes, separated by commas. A column can have a list of modifiers enclosed in
square brackets.

### Attributes

Usually, a column contains a single attribute. The only reason to include multiple attributes in a single column, is to
reference an entity that requires multiple attributes to be uniquely identified.

### Attribute

An attribute is column in a table. If the database column contains a foreign key, the attribute can be followed by a
list of attributes in parentheses. This is used to reference the columns in the foreign key table that are used to
uniquely identify the entity.

### Modifiers

A column can have a list of modifiers separated by colons.

### Modifier

A modifier is a key-value pair that is used to specify additional information about the column. The following modifiers
are supported:
- `unique`: A boolean value, 'true' if the columns in a unique key
- `filter`: A text value to filter rows. The value is an SQL WHERE clause, where '$' is replaced by the column name. Enclose in double quotes if the value contains characters other than 'a-zA-Z0-9_$'.



