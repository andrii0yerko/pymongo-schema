# pymongo-schema
A schema analyser for MongoDB, written in Python. 

This tools is inspired by [variety](https://github.com/variety/variety), with the following enhancements :

- extract the **hierarchical structure** of the schema 
- Several output options : python dictionnary, json, yaml or text
- **finer grained types**. ex: INTEGER, DOUBLE rather than NUMBER 
- ways to **filter** and **transform** the output schema

[![Build Status](https://travis-ci.org/pajachiet/pymongo-schema.svg?branch=master)](https://travis-ci.org/pajachiet/pymongo-schema)
[![Coverage Status](https://coveralls.io/repos/github/pajachiet/pymongo-schema/badge.svg?branch=master)](https://coveralls.io/github/pajachiet/pymongo-schema?branch=master)


# Install

Before distribution of a stable distribution on PyPi, you can install pymongo-schema from github : 
```shell
pip install --upgrade git+https://github.com/pajachiet/pymongo-schema.git
```
# Usage

```shell
Usage:
    pymongo-schema  -h | --help
    pymongo-schema  extract [--database=DB --collection=COLLECTION... --port=PORT --host=HOST --output=FILENAME --format=FORMAT... --quiet]
    pymongo-schema  transform [--input=FILENAME --filter=FILENAME --output=FILENAME --format=FORMAT... --columns=COLUMNS  --without-counts --quiet]
    pymongo-schema  tosql [--input=FILENAME --output=FILENAME --quiet]

Commands:
    extract                     Extract schema from a MongoDB instance
    transform                   Transform a json schema to another format, eventually filtering or changing columns outputs
    tosql                       Create a mapping from mongo schema to relational schema (json input and output)
```

To display full usage, with options description, run:
```shell 
pymongo-schema -h
```

TODO : add examples

# Schema

We define 'schema' as a dictionnary describing the structure of MongoDB component, being either a MongoDB instances, a database, a collection, an objects or a field. 
 
Schema are hierarchically nested, with the following structure :  



```python 
# mongo_schema : A MongoDB instance contains databases
{
    "database_name_1": {}, #database_schema,
    "database_name_2": # A database contains collections
    { 
        "collection_name_1": {}, # collection_schema,
        "collection_name_2": # A collection maintains a 'count' and contains 1 object
        { 
            "count" : int, 
            "object":  # object_schema : An object contains fields.            
             {
                "field_name_1" : {}, # field_schema, 
                "field_name_2": # A field maintains 'types_count_information
                                # An optional 'array_types_count' field maintains 'types_count' information for values encountered in arrays 
                                # An 'OBJECT' or 'ARRAY(OBJECT)' field recursively contains 1 'object'
                {
                    'count': int,
                    'prop_in_object': float,
                    'type': 'type_str', 
                    'types_count': {  # count for each encountered type  
                        'type_str' : 13,
                        'Null' : 3
                    }, 
                    'array_type': 'type_str',
                    'array_types_count': {  # (optional) count for each type encountered  in arrays
                        'type_str' : 7,
                        'Null' : 3
                    }, 
                    'object': {}, # (optional) object_schema 
                } 
            } 
        }
    }           
}
```
# Contributing - Limitations - TODO 
The code base should be easy to read and improve upon. Contributions are welcomed.

## Mixed types handling
pymongo-schema handles mixed types by looking for the lowest common parent type in the following tree.

<img src="type_tree.png" alt="type_tree" width=700/>

If a field contains both arrays and scalars, it is considered as an array. The 'array_type' is defined as the common parent type of scalars and array_types encountered in this field. 

TODO

- Improve mapping from Python type to name (TYPE_TO_STR dict)
    - see documentation: [bson-types](https://docs.mongodb.com/manual/reference/bson-types/), [spec](http://bsonspec.org/spec.html)

- Check a mongo scheme for compatibility to an sql mapping
- Handle incompatibilities

## Support Python 3 version

- fix encoding issues when exporting manually added non-ascii characters
- test for support of multiple Python versions

## Diff between schemas

A way to compare the schema dictionaries and highlights the differences.


## Test if a mongo schema can be mapped tosql

- test for the presence of mongo types in the mapping 
- look for mixes of list and scalar, that are currently not supported by mongo-connector-postgresql
- look for the presence of an '_id'

=> It may be donne directly in mongo-connector-postgresql doc_manager


## Adding fields in json/yaml outputs

- for example to add comments


## Other option to sort text outputs

- It is currently based on counts



## Tackle bigger databases
This code has been only used on a relatively small sized Mongo database, on which it was faster than Variety. 

To tackle bigger databases, it certainly would be usefull to implement the following variety's features :

- Analyze subsets of documents, most recent documents, or documents to a maximum depth.

## Tests
The codebase is not tested. It should not be trusted blindly.

## Distribution

Distribute in PyPi




