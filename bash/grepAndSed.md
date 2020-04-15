[HOME](README.md)

# grep
Using *-E* to enable regular expression utility
```
grep oa:: cckgui.tcl
```
# sed
## regular expression
* \\+: one or mode
* \*: 0 or more
## change results to what we want
```
sed 's/.*Physical: \([0-9]*\) MB.*/\1/g'
```
## remove the first lines for sed
```
sed  '1,3d' < test.log
```

# sort
# uniq
# awk
## remove duplicate line
```
awk '!seen[$0]++' filename
```
