#reference #memory #linux #k8s 

Does a Container Image Have an OS Inside
[https://iximiuz.com/en/posts/not-every-container-has-an-operating-system-inside/](https://iximiuz.com/en/posts/not-every-container-has-an-operating-system-inside/)

Buffer and Cache Memory in Linux
[https://www.baeldung.com/linux/buffer-vs-cache-memory](https://www.baeldung.com/linux/buffer-vs-cache-memory)

What Is Containers Architecture?
[https://medium.com/fintechexplained/what-is-a-container-architecture-design-54826e93fc18](https://medium.com/fintechexplained/what-is-a-container-architecture-design-54826e93fc18)

Based on my understanding, I'm pretty sure containers don't contain buffer and cache allocations because they don't actually run an instance of Linux.  They may had user space binaries from a distro, but ultimately they're all executing on the host OS.

According to this Reddit reply, there are no Linux buffers and cache by container, it's all part of the host OS.
[https://old.reddit.com/r/kubernetes/comments/1h6u7xr/does_every_container_in_a_cluster_have_its_own/m0glleo/](https://old.reddit.com/r/kubernetes/comments/1h6u7xr/does_every_container_in_a_cluster_have_its_own/m0glleo/)

So while a saw-tooth pattern in a container could be showing garbage collection or other memory collection taking place, it definitely doesn't represent Linux buffers or cache being cleared.

Individual containers will have page cache, but this is automatically freed by Linux as required, so this will not cause an OOM.  But that strongly suggest it's the kernel discarding pages to make room that causes the sawtooth pattern.

Linux Page Cache Basics
[https://www.thomas-krenn.com/en/wiki/Linux_Page_Cache_Basics](https://www.thomas-krenn.com/en/wiki/Linux_Page_Cache_Basics)

"The term, Buffer Cache, is often used for the Page Cache."