# dodocuments

### To pull the repository with submodule
 
`git clone --recurse-submodules -j8 git://github.com/foo/bar.git`

### To initialize and pull the submodules according to the revision in .gitmodules

`git submodule update --init --recursive`

### To update the submodule to latest remove revision

`git submodule update --remote --merge`

