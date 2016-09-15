# Mongo Commands
An intentionally non-comprehensive list of frequently used mongodb commands

For full documentation, check out the [real mongodb manual](https://docs.mongodb.org/manual)
______


**Start shell:**
```
$ mongo
```

**Login:**
```
> use vasp
switched to db vasp
> db.auth(<username>, <password>)
1
```

**Create a new user:**
```
> db.createUser(user, writeConcern)
```
`writeConcern` is optional. `user` is a document odbf the form:
```
{ user: "<name>",
  pwd: "<cleartext password>",
  customData: { <any information> },
  roles: [
    { role: "<role>", db: "<database>" },
    ...
  ]
}
```
see [this page](https://docs.mongodb.org/manual/reference/method/db.createUser/) for more details.

ex.
```
> db.createUser(
>   { user: "arthur_dent",
>     pwd: "theansweris42",
>     roles: [
>       { role: "read", db: "vasp" },
>       { role: "insert_test", db: "vasp" }
>     ]
>   }
> )
```

**Change a user's password:**
```
> db.changeUserPassword(<user>, <new password>)
```
ex.
```
> db.changeUserPassword("arthur_dent", "notheansweris43")
```

**Check a user's roles:**
```
> db.getUser(<username>)
```

**Grant additional roles to a user:**
```
> db.grantRolesToUser(<username>, roles)
```
ex. (to revoke from the example user (created above) all roles except `read`)
```
> db.grantRolesToUser("arthur_dent", {
>     [
>       { role: "read", db: "vasp" }
>     ]
>   }
> )
```

**Create collection:**
```
> db.createCollection(<name>, {options})
```
see [this page](https://docs.mongodb.org/manual/reference/method/db.createCollection/) for details on `options`

**Create a new role:**
```
> db.createRole( { createRole: <name>, privileges: [ <privileges> ], roles: [ <roles> ] } )
```
`privileges` is an array of the form:
```
[ { resource, actions }, { resource, actions }, ... ]
```
and `roles` is an array of roles from which the new role inherits privileges. `resource` is a document of the form:
```
{ db: <name>, collection: <name> }
```
and `actions` is an array of predefined... wait for it... actions. Examples include `insert`, `find`, `remove`, and `update`

ex.
```
> db.createRole(
>   {
>     createRole: "insert_test",
>     privileges: [
>       { resource: { db: "vasp", collection: "test" }, actions: ["insert"] }
>     ],
>     roles: []
>   }
> )
```

**Add field to document(s):**
```
> db.collection.update( { query }, { $set: { new_field: new_value } }, <upsert>, <multi> )
```
where <upsert> and <multi> are booleans. Set { multi: true } to update all documents matching the query. Set { upsert: true }  to create a new document if none match the query.

ex.
```
> db.update(
>  { 'pretty_formula': 'MoS2' },
>  { $set: { doi: '10.1038/nnano.2010.279' } }
> )
```

______
## Destructive Commands (**Use with caution**):
The following commands will remove data in the db, and as such should not be used experimentally, especially when the data are not your own
______

**Delete a user from a database:**
```
> db.dropUser(<username>)
```

**Delete one or a few entries from collection (BE CAREFUL):**
```
> db.collection.remove(<query>, {options})
```
see [this page](https://docs.mongodb.org/manual/reference/method/db.collection.remove/) for details on `options`

**Clear an entire collection (BE VERY CAREFUL):**
```
> db.collection.remove({})
```

**Delete a collection (BE UNBELIEVABLY CAREFUL):**
```
> db.collection.drop()
```
