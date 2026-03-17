# Git refs experiments
This project is for experimenting with different aspects of how git handles refs. 
In particular
- Custom Refs
- Git notes
- Garbage collection behavior


# Git notes tracked in Github
A git note was added to the initial commit `dfede48b4213a912c57198940b152932e25e4677`  
By using:
```sh
git note add dfede48b4213a912c57198940b152932e25e4677
```
Further more this note was signed by using

```sh
# Checkout commit for note
git switch -d refs/notes/commits
git commit --amend --no-edit -S
git update-ref refs/notes/commits HEAD
```

And finally pushed using:
```sh
git push origin "refs/notes/commits:refs/notes/commits"
```


The notes can be fetched using:
```sh
git fetch origin "refs/notes/commits:refs/notes/commits"
```

<!-- This seems to break push of ordinary branches. 
<--- It seems that this overrides the default behaviour
<--- and as such needs to add the default bahaviour pack somehow
<--- This should be able to be done by using
<--- ```sh
<--- git config set --append remote.origin.push "+refs/heads/*:refs/heads/*"
<--- ```
<--- However this does not exactly mirror default behaviour, as push tries to push to all branches.

Automatic fetching and push can be setup with:
```sh
git config set --append remote.origin.fetch "+refs/notes/commits:refs/notes/commits"
git config set --append remote.origin.push "+refs/notes/commits:refs/notes/commits"
```

Then ordinary pushs and fetches will automatically update local/remote notes
```sh
git fetch
git push
```
-->

Can add a remote for easier fetching and pushing notes:

```sh
git remote add --mirror=fetch -t "notes/commits" notes "git@github.com:MidnightRocket/git-refs-experiments.git"
git config set --append remote.notes.push "+refs/notes/commits:refs/notes/commits"
```

Can then simply do:
```sh
git fetch notes
git push notes
```
> [!WARNING]
> Unlike normal fetch, `git fetch notes` in this setup will overwrite locally stored notes without warning!


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


# Danling commits

## In Pull Request
In the pull request #1 a dangling commit has been created by a force push.  
The commit `4f7589e22189ee50e3ac2a22351c3e04ed67388a` is expected to be kept
because it is tracked by the pull request


## Totally unreferenced commit
This commit (`633f72bc4a0cbb2dc61d2515f6ea3f257f95cc5f`) was once pushed to `some-other-branch`,
but is now dangling. This serves as the sole reference to that commit.
It is expected that this commit is garbage collected. 

If that commit is not garbage collected eventually, then that might mean that Github has detected the mention of that commit in this README.
As such the following decodes into another dangling commit, which should be masked from Github in base64 encoding.
```sh
base64 -d <<EOF
NzZlZDIwNmZmMGM1OTc0MDZiZjFiOTgwODRjNmM2NDVhOWI1MDZiZg==
EOF
```

## Referenced in an Issue
Issue #2 mentions a dangling commit, which is expected to be kept, because it is referenced in that Issue.
The issue also mentions a short hash, to test if short hash longivity is well shorter. 


## Referenced in a commit message
The commit 5570e77cb5bb7c8801a748b20a2924381f7753f0 on branch `some-other-branch` references a series of squashed commits.
It will be interesting to see these commits will be garbage collected or not.
