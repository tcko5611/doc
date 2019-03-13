[HOME](README.md)

# grep
Using *-E* to enable regular expression utility

# sed
## change results to what we want
```
sed 's/.*Physical: \([0-9]*\) MB.*/\1/g'
```
## remove the first lines for sed
```
sed  '1,3d' < test.log
```
