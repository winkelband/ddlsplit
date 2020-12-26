# ddlsplit

Splits a DDL file generated by pgModeler
into single files by database objects (schema, extension, table etc.).
Comments on Tables are parsed as multiline top-level comments,
which can be utilized by [sphinx-sql](https://github.com/winkelband/sphinx-sql) (fork of [jackscodemonkey's sphinx-sql](https://github.com/jackscodemonkey/sphinx-sql)).

## Installation

`pip install ddlsplit`

## Usage

### Run as a program

```bash
ddlsplit <ddl.sql> <output-dir>
```

Or clone this repo and run the program with `poetry` from the cloned repository without installation: 

```bash
poetry install
poetry run ddlsplit <ddl.sql> <output-dir>
```

### Use as a module

Make sure, `ddlsplit` is in your `$PATH` or project's namespace!

```python
import ddlsplit


ddl_code = '''
...
'''
```

## Reverse Engineering A Database & Its Documentation

`pgModeler → ddlsplit → sphinx w/ sphinx-sql`

If you maintain your database model with a graphical data modeling tool like pgModeler,
you can build a pipeline to automatically build your documentation with [sphinx-sql](https://github.com/winkelband/sphinx-sql),
e.g. by calling `pgModeler` and `ddlsplit` from your sphinx' Makefile.

This approach lets you keep the documentation of your SQL code inside the code itself,
thus reducing overhead costs by keeping code and documentation residing in multiple places in sync.

```bash
# Optional: Reverse engineering an existing database:
pgmodeler-cli \
        -platform offscreen \
        --export-to-file \
        --import-db \
        --ignore-errors \
        --conn-alias local-db \
        --input-db demodb \
        --output demo.sql \
        --pgsql-ver 12.0

# Export pgModeler's dbm to sql file:
pgmodeler-cli \
        -platform offscreen \
        --export-to-file \
        --input demo.dbm \
        --output demo.sql \
        --pgsql-ver 12.0
```

Example for top level comment for a table with pgModeler:

```
Purpose:
This a new table to show how auto documentation can add new objects quickly.
Dependent Objects:
	Type	|	Name
	Schema	|	my_test_schema
ChangeLog:
	Date	|	Author	|	Ticket	|	Modification
	2020-12-26	|	Developer_2	|	T-247	|	Initial Definition

```

Results in:

```SQL
COMMENT ON TABLE public.find IS E'Purpose:\nThis a new table to show how auto documentation can add new objects quickly.\nDependent Objects:\n\tType\t|\tName\n\tSchema\t|\tmy_test_schema\nChangeLog:\n\tDate\t|\tAuthor\t|\tTicket\t|\tModification\n\t2020-12-26\t|\tDeveloper_2\t|\tT-247\t|\tInitial Definition';
```

## Developing

```bash
poetry install
poetry build
```

## Contributing
