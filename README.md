<br>

<h1 align="center"> Fix Pixeluvo version Steam for Linux :penguin: </h1>
<h3 align="center"> Auto configuration failed </h3>

<br><br>

I'm using Pop!_OS 22.04 LTS (Ubuntu-based Jammy Jellyfish). When opening Pixeluvo via Steam, nothing happens. When attempting to open it via the terminal, the following error occurs:

<br>

~~~sh
/steamapps/common/Pixeluvo
╰─ ./pixeluvo
Using Pixeluvo Shipped Qt Libraries
Auto configuration failed
139864210065344:error:25066067:DSO support routines:DLFCN_LOAD:could not load the shared library:dso_dlfcn.c:187:filename(libproviders.so): libproviders.so: cannot open shared object file: No such file or directory
139864210065344:error:25070067:DSO support routines:DSO_load:could not load the shared library:dso_lib.c:244:
139864210065344:error:0E07506E:configuration file routines:MODULE_LOAD_DSO:error loading dso:conf_mod.c:285:module=providers, path=providers
139864210065344:error:0E076071:configuration file routines:MODULE_RUN:unknown module name:conf_mod.c:222:module=providers
~~~
<br>

Searching about the error I found a post on github gist by user [craigvantonder](https://gist.github.com/craigvantonder/1d8b64b744363e9fa4108947c1758e92) talking about the error and sharing the fix, so I took the fix he made and added it to the "pixeluvo" file of the steam version.

<br>

### Fix
~~~sh
# FIX Auto configuration failed Steam Version
    if [ -z ${OPENSSL_CONF+x} ];
    then
        export OPENSSL_CONF=/dev/null
    fi
# END FIX Auto configuration failed Steam Version
~~~

<br><br>

### Fix Steam
Inside Steam, right click on Pixeluvo > Properties > Installed Files > Explore.<br>
Copy the code below open the file "pixeluvo" with text editor and replace and save.

<br>

~~~sh
#!/bin/bash

if [ `getconf LONG_BIT` = "64" ]
then
    LIBS=./libs64
    QTLIBS=./libs64/Qt
    BIN=./bin/Pixeluvo64
else
    LIBS=./libs32
    QTLIBS=./libs32/Qt
    BIN=./bin/Pixeluvo32
fi

# Check if the system has Qt Libraries installed
if (( $(ldconfig -p | grep QtDeclarative -i -c) > 0 )) && (( $(ldconfig -p | grep QtSvg -i -c) > 0 ))
then
    echo "Using System Qt Libraries"
else
    echo "Using Pixeluvo Shipped Qt Libraries"
    export QT_PLUGIN_PATH=""
    export LD_LIBRARY_PATH=$QTLIBS:"$LD_LIBRARY_PATH"
    
# FIX Auto configuration failed Steam Version

    if [ -z ${OPENSSL_CONF+x} ];
    then
        export OPENSSL_CONF=/dev/null
    fi
# END FIX Auto configuration failed Steam Version

fi

# Run Pixeluvo
export LD_LIBRARY_PATH=$LIBS:"$LD_LIBRARY_PATH"
$BIN "$@"
~~~
<br><br>

### Fix package .deb [1.6 only](https://gist.github.com/craigvantonder/1d8b64b744363e9fa4108947c1758e92)

<br><br><br><br>

### References

- [path_Application_start.md](https://gist.github.com/craigvantonder/1d8b64b744363e9fa4108947c1758e92)
- [bazelbuild](https://github.com/bazelbuild/rules_closure/issues/351#issuecomment-854628326)
- [aisingapore](https://github.com/aisingapore/TagUI/issues/787#issue-602217640)
- [phantomjs](https://github.com/ariya/phantomjs/issues/15449)
- [SteamDB](https://steamdb.info/app/314500/history/)

<br><br>
