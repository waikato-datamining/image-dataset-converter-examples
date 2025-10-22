The following pipeline components can be used for storing and retrieving data
from temporary storage (i.e., a dictionary) that is available through the session 
object of the pipeline:

* `delete-storage` - filter that removes the name storage item
* `from-storage` - reader retrieves the named storage item
* `set-storage` - filter that updates the storage with the data passing through
* `to-storage` - writer that updates the storage with the data arriving

For annotations, you can use these specialized filters:

* `annotations-from-storage` - replaces the annotations of the current container with the ones from storage
* `annotations-to-storage` - pushes the annotations of the current container into internal storage
