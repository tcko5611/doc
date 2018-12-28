[HOME](README.md)

# part of the depository
```
git clone --depth 1 --no-checkout --filter=blob:none \
  "file://$(pwd)/server_repo" local_repo
cd local_repo
git checkout master -- mydir/
```

