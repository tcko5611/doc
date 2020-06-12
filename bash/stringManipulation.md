# Bash String Manipulation - Length, Substring, Find and Replace

## Length of String

```
#! /bin/bash
var="Welcome to the geekstuff"
echo ${#var}
```

## Substring
```
${string:position:length}
```
example
```
$ cat substr.sh
#! /bin/bash

var="Welcome to the geekstuff"

echo ${var:15}
echo ${var:15:4}

$ ./substr.sh
geekstuff
geek
```

## Shortest Substring Match
Delete the shortest match of substring from front of string
```
${string#substring}
```
Delete the shortest match of substring from back of string
```
${string%substring}
```
example 
```
$ cat shortest.sh
#! /bin/bash

filename="bash.string.txt"

echo ${filename#*.}
echo ${filename%.*}

$ ./shortest.sh
After deletion of shortest match from front: string.txt
After deletion of shortest match from back: bash.string
```
## Longest SubString Match
Delete the longest match of substring from front of string
```
${string##substring}
```
Delete the longest match of substring from back of string
```
${string%%substring}
```
example 
```
#! /bin/bash

filename="bash.string.txt"

echo "After deletion of longest match from front:" ${filename##*.}
echo "After deletion of longest match from back:" ${filename%%.*}

$ ./longest.sh
After deletion of longest match from front: txt
After deletion of longest match from back: bash
```

## Find and Replace
Replace only first match
```
${string/pattern/replacement}
```
example 
```
$ cat firstmatch.sh
#! /bin/bash

filename="bash.string.txt"

echo "After Replacement:" ${filename/str*./operations.}

$ ./firstmatch.sh
After Replacement: bash.operations.txt
```
Replace all matches
```
${string//pattern/replacement}
```
example
```
$ cat allmatch.sh
#! /bin/bash

filename="Path of the bash is /bin/bash"

echo "After Replacement:" ${filename//bash/sh}

$ ./allmatch.sh
After Replacement: Path of the sh is /bin/sh
```
Replace beginning
```
${string/#pattern/replacement}
```
Replace ending
```
${string/%pattern/replacement}
```
example
```
$ cat posmatch.sh
#! /bin/bash

filename="/root/admin/monitoring/process.sh"

echo "Replaced at the beginning:" ${filename/#\/root/\/tmp}
echo "Replaced at the end": ${filename/%.*/.ksh}

$ ./posmatch.sh
Replaced at the beginning: /tmp/admin/monitoring/process.sh
Replaced at the end: /root/admin/monitoring/process.ksh
```
