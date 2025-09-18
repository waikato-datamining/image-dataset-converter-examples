The following pipeline components can be used for storing and retrieving data
from temporary storage (i.e., a dictionary) that is available through the session 
object of the pipeline:

* `from-storage` - reader retrieves the named storage item
* `set-storage` - filter that updates the storage with the data passing through
* `to-storage` - writer that updates the storage with the data arriving
