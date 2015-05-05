
```
#!/usr/bin/env python

import nzbfetch
import sys
import os
import time
import stat

if len(sys.argv) > 0:
    filename = sys.argv[1]

try:
    os.stat(filename)
except:
    print "File not found"
    sys.exit(1)

try:                                                                                                                                                                                          
    os.mkdir("tmp")                                                                                                                                                                         
    os.mkdir("complete")                                                                                                                                                               
except:                                                                                                                                                                                     
    pass                                                                                                                                                                                          

fetcher = nzbfetch.NZBFetch()
fetcher.addserver("server.example.org", 119, "username", "password", 3, 0)
fetcher.connect()


file = nzbfetch.parse(filename)
file.set_temporary_path("tmp/")
file.set_storage_path("complete/")

files = file.list_files()
print "Downloading files.. "
for fileinfo in files:
    fetcher.download(file, fileinfo['id'])


while 1:
    time.sleep(1)

```