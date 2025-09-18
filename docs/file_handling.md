# Reading/writing text files

* `from-text-file` - this reader reads the file line by line and forwards them
* `to-text-file` - writes the incoming strings to the specified text file


# Listing files

* `list-files` - simply lists files in a directory forwards the list
* `poll-dir` - polls a directory for files to process, can move or delete 
  them after they were processed by the specified base-reader
* `watch-dir` - uses a file-system watchdog to look for changes to files 
  (events: created or modified) and forwards these to the base-reader,
  can move or delete these files after processing as well

# Others

* `delete-files` - deletes the incoming files
* `move-files` - moves the incoming files into the specified target directory
