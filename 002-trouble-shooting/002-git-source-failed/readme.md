#  Git source failed. Could not read Username for 'https://github.ibm.com': No such device or address

## Issue

Git source failed. 

Error running git [pull --recurse-submodules=yes origin]: exit status 1\nfatal: could not read Username for 'https://github.ibm.com': No such device or address

## Log

{"level":"warn","ts":1579497963.0612688,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: \"KO_DATA_PATH\" does not exist or is empty"}
{"level":"error","ts":1579497965.3964727,"logger":"fallback-logger","caller":"git/git.go:35","msg":"Error running git [fetch --depth=1 --recurse-submodules=yes origin 34dc19044e60fbba09edf38ad5255d3b35fec21a]: exit status 128\nfatal: could not read Username for 'https://github.ibm.com': No such device or address\n","stacktrace":"github.com/tektoncd/pipeline/pkg/git.run\n\t/go/src/github.com/tektoncd/pipeline/pkg/git/git.go:35\ngithub.com/tektoncd/pipeline/pkg/git.Fetch\n\t/go/src/github.com/tektoncd/pipeline/pkg/git/git.go:88\nmain.main\n\t/go/src/github.com/tektoncd/pipeline/cmd/git-init/main.go:36\nruntime.main\n\t/usr/local/go/src/runtime/proc.go:198"}
{"level":"error","ts":1579497965.9346163,"logger":"fallback-logger","caller":"git/git.go:35","msg":"Error running git [pull --recurse-submodules=yes origin]: exit status 1\nfatal: could not read Username for 'https://github.ibm.com': No such device or address\n","stacktrace":"github.com/tektoncd/pipeline/pkg/git.run\n\t/go/src/github.com/tektoncd/pipeline/pkg/git/git.go:35\ngithub.com/tektoncd/pipeline/pkg/git.Fetch\n\t/go/src/github.com/tektoncd/pipeline/pkg/git/git.go:91\nmain.main\n\t/go/src/github.com/tektoncd/pipeline/cmd/git-init/main.go:36\nruntime.main\n\t/usr/local/go/src/runtime/proc.go:198"}
{"level":"warn","ts":1579497965.934693,"logger":"fallback-logger","caller":"git/git.go:92","msg":"Failed to pull origin : exit status 1"}
{"level":"error","ts":1579497965.9368465,"logger":"fallback-logger","caller":"git/git.go:35","msg":"Error running git [checkout 34dc19044e60fbba09edf38ad5255d3b35fec21a]: exit status 128\nfatal: reference is not a tree: 34dc19044e60fbba09edf38ad5255d3b35fec21a\n","stacktrace":"github.com/tektoncd/pipeline/pkg/git.run\n\t/go/src/github.com/tektoncd/pipeline/pkg/git/git.go:35\ngithub.com/tektoncd/pipeline/pkg/git.Fetch\n\t/go/src/github.com/tektoncd/pipeline/pkg/git/git.go:94\nmain.main\n\t/go/src/github.com/tektoncd/pipeline/cmd/git-init/main.go:36\nruntime.main\n\t/usr/local/go/src/runtime/proc.go:198"}
{"level":"fatal","ts":1579497965.9369557,"logger":"fallback-logger","caller":"git-init/main.go:37","msg":"Error fetching git repository: exit status 128","stacktrace":"main.main\n\t/go/src/github.com/tektoncd/pipeline/cmd/git-init/main.go:37\nruntime.main\n\t/usr/local/go/src/runtime/proc.go:198"}

## Image

![Issue](images/01-issue.png?raw=true "Issue")

## Reason

The username to access `https://github.ibm.com` could not be found

## Solution

Create a Secret to Git Server in Tekton dashboard as follows.

1. Create Secret as given below. 

Here `jeyagandhi` is a username to access `https://github.ibm.com`

![Create secret](images/02-solution.png?raw=true "Create secret")

2. Created secret should like like the below

![secret](images/03-solution.png?raw=true "secret")
