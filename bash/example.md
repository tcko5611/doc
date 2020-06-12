# for a example

line to indicate the it's a sh script
```
#!/bin/sh
```
grep the arguments has **--debug** then **set -x** for debugging mode. 
```
echo " $@ " |grep " --debug " >/dev/null && set -x 
```
define some variables
```
primaryArchs="linux64|amd64|linux"
secondaryArchs="suse64|suse32"
allArchs="$primaryArchs|$secondaryArchs"

unset CDPATH
buildToolsBin=`dirname $0`
```
using pipe and grep true or false to determine extract variable
```
echo $buildToolsBin |grep '^/' >/dev/null || \
    buildToolsBin=`(cd $buildToolsBin; /bin/pwd)`
buildTools=`dirname $buildToolsBin`
buildToolsParent=`dirname $buildTools`

eval `$buildToolsBin/getGlobalVars QSC DefaultArch P4Binary RupeeBinary DefaultModule`

defaultQSC=$QSC
unset QSC

```

function
```
USAGE() {
    [ -n "$1" ] && echo "$1"
    Archs=`echo "$primaryArchs" |sed -e 's/|/ | /g'`
    cat << EOM
...
EOM
   [ -n "$1" ] && exit 1 || exit 0
}
```
eval will expplict extract the command and then execute it,
for example if we have "-arch linux64", then archArg=linux64
```
for arg in "$@"; do
    if [ -n "$nextArg" ]; then
        eval "$nextArg=$arg"
        unset nextArg
        continue
    fi
    case $arg in
        --arch=*|arch=*) archArg=`echo $arg |cut -d= -f2`;;
        -a|-arch) nextArg=archArg;;
        ...
	esac
done
```
-n: not empty string
-z: empty string
```
[ -n "$QSC" ] && QSC=`echo $QSC |tr [a-z] [A-Z]`

[ -z "$TMP" ] && TMP=$TMPDIR
[ -z "$TMP" ] && TMP=/tmp
[ -z "$USER" ] && USER=$LOGNAME

```

* tr: trasnlate character
* cut: remove sections from each line of files, 
  * -d, delimiter, 
  * -f2, select field list
  * -c2-, select charater from 2 to end


```
if [ -n "$SYNOPSYS_CUSTOM_INSTALL" ]; then
    SCroot=`dirname $SYNOPSYS_CUSTOM_INSTALL`
    # Remove Synopsys Custom libdirs from $LD_LIBRARY_PATH
    unset __noSC_LD_LIBRARY_PATH__
    for L in `echo $LD_LIBRARY_PATH |tr : ' '`; do
        case $L in
            $SCroot*) ;;
            *) __noSC_LD_LIBRARY_PATH__=$__noSC_LD_LIBRARY_PATH__:$L ;;
        esac
    done
    if [ -n "$__noSC_LD_LIBRARY_PATH__" ]; then
        LD_LIBRARY_PATH=`echo $__noSC_LD_LIBRARY_PATH__ |cut -c2-`
        export LD_LIBRARY_PATH
```
