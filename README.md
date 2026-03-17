# Git refs experiments
This project is for experimenting with different aspects of how git handles refs. 
In particular
- Custom Refs
- Git notes
- Garbage collection behavior



# Custom "Hidden" refs
The following commit `8fbca4e9d330c216347bac371113ae707ba00499` represents a ref
which is pushed to `refs/custom/hidden-ref`.
This should not be garbage collected. 
To fetch this ref use
```sh
git fetch origin "refs/custom/hidden-ref"
```

This was pushed using

```sh
git push origin "refs/custom/hidden-ref:refs/custom/hidden-ref"
```
