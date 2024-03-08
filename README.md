![Info Database](./.vamos/info-database.jpg)

--------------------------------------------------------------------------------

# Info Database
Info Database (`idb`) is a bash script to manage simple databases controlling environment settings. Typically, `idb` is not directly used 
from `bash` command shell but as a helper for other scripts which manage
environment settings.

## Example: Working Directory

`wd` (working directory) is a bash script to assign a label to the path
of the current directory. Its basic use is as follows:

```
    $ wd -! work: 'my working folder'  # define label work:
    $ cd                               # change to home folder
    $ wd work:                         # change to my working folder                                  
```   
Script `wd` internally uses the `idb` tool to create an info database `$ETC/workdir` and to store a data record as file `$ETC/workdir/work` for storing
path information related to label `work`.

When `wd work:` is invoked, `wd` consults `idb` to retrieve the path information from record `$ETC/workdir/work` in order to change the current
directory to the path retrieved from the info database (related to label `work:`).

Tool `wd` uses environment variable `ETC` to specify the path to the info database. If `ETC` is not defined in your system you can create a `~/etc`
directory in your home folder (with read/write permissions) and add the following line to your `bash` profile script (resource/startup script, e.g., `.bashrc`):

```
    export ETC='~/etc'
```


# How `idb` Works

## Info Database Creation

Let the info database name be `my-idb` (in fact any name of choice will do the job, e.g. `workdir` in the `wd` example case), and let's have an environment variable `MYIDB` holding the full path of a specific info database. An info database `my-idb` is created by invoking the following command:

```
    $ MYIDB=/path-to/my-idb  # full path to info database 'my-idb'
    $ idb -c $MYIDB          # create info database 'my-idb' at path 'path-to' 
```

## Storing an Info Database Record

An info database is organized in terms of records which are adressed by keys. A record, on the other hand, is simply a list of tags asigned with values. The syntax to store a given `<tag>` assigned with a `value>` in a record adressed by `<key>` is:

```
    $ idb -s <dbase> <key> <tag> <val>  # store <tag>/<value> pair in <key> record
```

Referring to the example above, we use label `work` as database key for a record storing the following tag/value pairs:

```
   tag: `dir`  <-> value: `path-to-workdir`
   tag: `info` <-> value: `'my working folder'`
```

The following `idb` commands stores the two tag/value pairs into a data record adressed by key 'work':

```
    $ idb -s $MYIDB work dir path-to-workdir
    $ idb -s $MYIDB work info 'my working folder'
```

Since info database records are stored as files in a directory, which has a name matching to the key, we can easily investigate, how `idb` stores 
the information.

```
    $ cat $MYIDB/work
    dir='path-to-workdir'
    info='my working folder'
```

## Recalling an Info Database Record

To recall the `<value>` information related to a given `<tag>` of a record adressed by `<key>` script `idb` is called with the recall option `-r`, which prints the `<value>` information to standard output.

```
    $ idb -r <dbase> <key> <tag> 
``` 

To assign a retrieved value to an environment variable VALUE we use back-quotes.

```
    $ VALUE=`idb -r <dbase> <key> <tag>` 
``` 

Referring to the previous example, recalling the `dir` and `info` values and assigning it to environment variables `DIR` and `INFO` is achieved as follows: 

```
    $ DIR=`idb -s $MYIDB work dir`     # retrieve dir value
    $ INFO=`idb -s $MYIDB work info`   # recall info value
```

## Conclusions

* To store/recall settings/configuration information in a bash environment, database formats like JSON (Java Script Object Notation), YAML (Yet Another Markdown Language), TOML (Tom's Obvious, Minimal Language) and others have been developped.
* Though powerful and ubiquitous in use require tools running on native code or depending on pre-requisites, like proper Python/Jave installation
* With `idb` settings/configuration information can be stored/recalled in `bash` shells without pre-requisites.
* The utilization of `idb` is mainly as helper tool in other bash scripts. 
* Since `bash` shells are now available on all three main platforms (Linux, Mac-OS and Windows/WSL), `idb` provides minimal basic database functionality running on all three common platforms.  