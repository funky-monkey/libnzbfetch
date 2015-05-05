libnzbfetch is a cross platform c library to download files from usenet using a nzb file. The focus is to make it as efficient as possible. At this point it uses around 5 a 6% cpu (Core 2 duo 2.16) while downloading with +/- 13mbit.

It currently supports multiple servers with different priorities and SSL connections.

It is still in early development, to try it out use:
```
svn co http://libnzbfetch.googlecode.com/svn/trunk libnzbfetch
cd libnzbfetch
sh autogen.sh
./configure --with-expat=<expat path>
make
```

An example client can be found in the example/ directory
Python bindings to the library can be found in the bindings/python directory. An example of a python client can be found [here](Python.md)



Example:
```
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
#include <assert.h>
#include <unistd.h>

#import <nzb_fetch.h>

int main(int argc, char **argv)
{
    nzb_fetch *fetcher;
    nzb_file *nzb_file;
    nzb_file_info **files;
    
    int num_files;
    int i;
    
    if (argc < 4)
    {
        printf("Usage: \n");
        printf("%s <nzb_file> <server> <username> <password> <connections>\n",
               argv[0]);
        exit(1);
    } 
    
    fetcher = nzb_fetch_init();

    nzb_fetch_add_server(fetcher, argv[2], 119,
                        argv[3], argv[4], 2, 0);
    
    
    nzb_fetch_connect(fetcher);

    nzb_file = nzb_fetch_parse( argv[1]);
    nzb_fetch_storage_path(nzb_file, strdup("tmp/complete/"));
    nzb_fetch_temporary_path(nzb_file, strdup("tmp/chunks/"));

    num_files = nzb_fetch_list_files(nzb_file, &files);

    for (i = 0; i < num_files; i++)
    {
        printf("Adding %s to the queue\n", files[i]->filename);
        nzb_fetch_download(fetcher, files[i]);
    }
    
    while(1)
    {
        sleep(1);
        printf(".\n");
    }

    return 0;
}
```