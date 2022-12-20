# dep_postgres

These functions address the difficulty of working with dependencies in postgres. To change the schema of a view, table, function, etc. that has dependencies requires that you drop it using cascade (recursively drop all objects that use it). This desentivizes creating useful dependencies. 


### Work Flow:
1. Store all dependencies on an object (table, view, function) to a cache.
2. Drop the object with cascade.
3. Redefine the object how you please.
4. Restore dependencies on the object using the cache. Go and fix dependencies that couldn't be restored and try again.
6. Everything's restored!


### Objects:
#### Functions:

- `dep_save(schema_name, object_name)` - Save all recursive dependencies on the object, including the source object.
- `dep_restore(include_source_object?)` - Restore all saved dependencies. Restore the source object if true is passed as an argument.
- `dep_unsave()` - Clear the saved cache.
- `dep_get_objects_recursive(schema_name, object_name)` - Get the recursive dependencies on an object.


#### Views:
- `dep_saved` - The cache of saved object. You can query this to see what's saved.
- `dep_objects` - A view of non-recursive dependencies on an object. You can query this to check immediate dependencies.


### Installation:
Just copy the code in src.psql and execute it in your database!


### Usage:
```
select * from dep_save('public','viewa'); -- Save dependencies

drop view viewa cascade; -- Drop all of your objects

create or replace viewa as select col1 from viewb; -- Modify your source object

select * from dep_restore(); -- Restore the dependencies

select dep_unsave(); -- Clear the save cache
```


### Limitations:
- There are inefficencies in the code I wrote (a double for loop, so I didn't have to deal with restoring dependencies in the proper order).
- It only works with tables, views, and functions. I might add triggers if I end up using those.
- Table definitions aren't stored. You can save dependencies on a table, but tables won't be restored.
- There's no way to restore your cache, so be careful when unsaving!
- I'm new to postgres, so there may be issues I'm unaware of. I made these because it wasn't fun building a data model having to drop things constantly. 
- I made this for how I use postgres, I'm sure there are a lot of additions that can be made so feel free to contribute!


### Star me:
- If this helped you can you give the project a star? I'd like to know if this helped other people.
