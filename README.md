# Linux Namespaces
A Namespace (ns) wraps some global system resource to provide resource isolation.

## Linux support multiple NS types
* Mount | CLONE_NEWNS | 2002
* UTS | CLONE_NEWUTS | 2006
* IPC | CLONE_NEWIPC | 2006
* PID | CLONE_NEWPID | 2008
* Network | CLONE_NEWNET | 2009
* User | CLONE_NEWUSER | 2013
* Cgroup | CLONE_NEWCGROUP | 2016
* Time (proposed)

## Namespaces are Linux-specific feature
* For each NS type
    * Multiple instances of NS may exist on a system
        * At system boot, there is one instance of each NS type (initial namespace)
    * Each process resides in one NS instance.
    * To process inside NS instance, it appears that only they can see/modify corresponding global resource.
        * Processes are unaware of other instances of resource.
        
* When new processes is created via fork(), it resides in same set of NSs as parent.






one of the most interesting types of namespaces is `user namespaces` and that's what I'm gonna sort of drill down to it

in the second piece because user namespaces sort of bring all the other namespaces together in a way that let us

do some quite powerful things things like unprivileged containers for example

How its possible to be super user inside a  container

> super user inside
> Unprivileged outside

### Unprivileged containers


## Learning Resource
* https://lwn.net/Articles/531114/