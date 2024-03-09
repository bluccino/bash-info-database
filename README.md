![Info Database](./.vamos/info-database.jpg)

--------------------------------------------------------------------------------

# Info Database
Info Database (`idb`) is a bash script to manage simple databases for store/recall of configuration information. Typically, `idb` is not directly invoked from `bash` command shell. Rather, `idb` might be consulted as a helper in other scripts, which manage a configurable environment.

## Example: Working Directory

Imagine a bash script `wd` (working directory) which is capable of two tasks:

	1) assign a given label with the path of the current directory
	2) change the current directory according to the path assigned with a given label

Since, for the second task, script `wd` needs to change the current directory, it cannot run in a sub shell. Thus, we must `source` the
script, invoking it by either `$ source wd ...` or `$ . wd ...`. 
The syntax for our `wd` command might be designed as follows:

    $ . wd -! work: 'my working folder'  # define label work:
    $ cd                                 # change to home folder
    $ . wd work:                         # change to my working folder                                  

Script `wd` might     internally use the `idb` tool to create an info database `$ETC/workdir` in order to store a data record as file `$ETC/workdir/work` for memorizing path information related to label `work`.

When `wd -! work:` is invoked, `wd` can consult `idb` to recall the path information from record `$ETC/workdir/work` in order to change the current
directory to the path retrieved from the info database (related to label `work:`).

Tool `wd` might use environment variable `ETC` to specify the path to the info database. If `ETC` is not defined in the system, a directory `~/etc`
may be created in the home folder (with read/write permissions) and the following line can be added to the `bash` profile script (resource/startup script, e.g., `.bashrc`):

```
    export ETC='~/etc'
```


# How `idb` Works

## Info Database Creation

Let the info database name be `my-idb` (in fact any name of choice will do the job, e.g. `workdir` in the `wd` example), and let's have an environment variable `MYIDB` holding the full path of a specific info database. An info database `my-idb` is created by invoking the following command:

```
    $ MYIDB=/path-to/my-idb  # full path to info database 'my-idb'
    $ idb -c $MYIDB          # create info database 'my-idb' at path '/path-to' 
```

## Storing an Info Database Record

An info database is organized in terms of records, which are adressed by keys. A record, on the other hand, is simply a list of attribute tags asigned with values. The syntax to store a given attribute `<tag>` assigned with a `<value>` in a record adressed by `<key>` is:

```
    $ idb -s <dbase> <key> <tag> <val>  # store <tag>/<value> pair in <key> record
```

Referring to the example above, we use label `work` as database key for a record storing the following tag/value pairs:

```
   tag: `dir`  <-> value: `path-to-workdir`
   tag: `info` <-> value: `'my working folder'`
```

The following `idb` commands store the two tag/value pairs into a data record adressed by key 'work':

```
    $ idb -s $MYIDB work dir path-to-workdir
    $ idb -s $MYIDB work info 'my working folder'
```

Since info database records are stored as files in a directory, which has a name matching the key, we can easily investigate, how `idb` stores 
the information.

```
    $ cat $MYIDB/work
    dir='path-to-workdir'
    info='my working folder'
```  

## Recalling an Info Database Record

To recall the `<value>` information related to a given `<tag>` of a record adressed by `<key>`, script `idb` is called with the recall option `-r`, which prints the related `<value>` information to standard output.

```
    $ idb -r <dbase> <key> <tag>
    <value>
``` 

To assign a retrieved value to an environment variable VALUE we use back-quotes.

```
    $ VALUE=`idb -r <dbase> <key> <tag>` 
``` 

Referring to the previous example, recalling the `dir` and `info` values and assigning it to environment variables `DIR` and `INFO` is achieved as follows: 

```
    $ DIR=`idb -r $MYIDB work dir`     # retrieve dir value
    $ INFO=`idb -r $MYIDB work info`   # recall info value
```

## Listing The Info Database Contents

The list of a tag/value assignments of a record can be listed as follows:

```
    $ idb -l $MYIDB
    work:
    dir: path-to-workdir
    info: my working folder
``` 

## Other `idb` Commands

Use `$ idb --help` to see all supported commands.

```
    $ idb --help
    usage: manage/access info database (version 1.0.2)
      idb -s <dbase> <key> <tag> <val> # store value in info database
      VAL=`idb -r <dbase> <key> <tag>` # recall value from info database
      idb -c <dbase>                   # create info database
      idb -d <dbase> <key>             # delete IDB record related to key
      idb -l <dbase>                   # list contents of info database
      idb -l <dbase> <key>             # list IDB record related to key
      idb -?                           # show usage
      idb --help                       # comprehensive help
      idb --version                    # print version
    example: create information database
      idb -c $ETC/@boards              #create @boards info database
    example: store board information to info database
      idb -s $ETC/@boards n1 BOARD nrf52dk_nrf52832
      idb -s $ETC/@boards n1 SEGGER 682805980
      idb -s $ETC/@boards n1 BRDID n1
      idb -s $ETC/@boards n1 BRDINFO "Nordic 832dk #1"
    example: recall board information from info database
      BOARD=`idb -r $ETC/@boards n1 BOARD`
      SEGGER=`idb -r $ETC/@boards n1 SEGGER`
      BRDID=`idb -r $ETC/@boards n1 BRDID`
      BRDINFO=`idb -r $ETC/@boards n1 BRDINFO
``` 


## Conclusions

* To store/recall settings/configuration information in a bash environment, database formats like JSON (Java Script Object Notation), YAML (Yet Another Markdown Language), TOML (Tom's Obvious, Minimal Language) and others have been developped.
* Though existing tools, working with those database formats, are powerful and ubiquitous in use, they are either running with native code or depend on pre-requisites, like a proper Python/Jave installation
* One big advantage of `idb` is that settings/configuration information can be stored/recalled in `bash` shells without pre-requisites.
* The utilization of `idb` is mainly as a helper tool for other bash scripts which manage a configurable environment without the requirement of pre-requisites other than a standard `bash` environment.
* Since `bash` shells are now available on all three main platforms (Linux, Mac-OS and Windows/WSL), `idb` provides minimal basic database functionality running on all three common platforms.  