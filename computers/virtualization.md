Virtualization vs Emulation
===========================

Keywords:
- Dynamic translation
    - Binary translation [1]
- Types of virtualization (para-virtualization)
- Hardware assisted virtualization

Within the realm of abstracting away physical hardware in favor of software-
assisted we come across two seemingly inter-related terms: emulation and
virtualization. On the surface, both of these appear to overlap confusingly.
In this document, I'll attempt to add points of differences and commonalities
between the two.

**Virtualization**
Let's start with some theory. Virtualization, in the sense it is referred to by
modern literature, usually means providing an illusion of unrestricted access
to physical hardware while actually controlling it.

An example would be virtual memory. The running process understands that it 'owns'
the whole address space and in effect, it is free to use any part of it. But the
OS acts as a gateway defining the 'mapping' between the memory the process thinks
it is using and the actual memory it ends up using. In this sense, the OS is a
supervisor which funnels access to hardware according to some sensible criteria.
The process still uses the *physical* memory and not something the OS cooked up
but it ends up using it in a manner prescribed by some supervisory entity.

Taking this definition of a *virtualized* resource and applying it more broadly
to the OS itself leads us to the concept of hypervisors. In a naïve sense, you
could think of a hypervisor as a level on top of the OS. In a vanilla
configuration, the OS is the ultimate authority standing between the
application and the hardware. In a virtualized system, the hypervisor
multiplexes the hardware to be allocated to the running applications (which tend
to be OSes themselves).

[1] http://www.anandtech.com/show/2480/4
