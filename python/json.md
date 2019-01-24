[HOME](README.md)

# Basic structure of json
* list: ['a', 'b']
* dict: {'aa':1, 'bb', 3.0}
* complete: ["foo", {"bar": ["baz", null, 1.0, 2], "test" : 2.0}]

# Load Json in Python
```
import json
with open('ff', 'r') as f:
  jP = json.load(f)
print(jP['license'][0]['prod_name'])
```
# Save Json in Python
```
import json
with open('ff', 'w') as f:
  f.write(json.dumps(x))
```

