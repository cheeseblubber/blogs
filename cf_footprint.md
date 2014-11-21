## Cloud Foundry Development Hardware Requirements

### Abstract
How much RAM, Disk, and CPU are needed for a Cloud Foundry deployment? We believe you need this much at a minimum:

* 46GB RAM
* 2 cores (they don't need to be fast) (hyperthreading doesn't count)
* 466GB disk

Requirements increase as you add additional services (e.g. MySQL, GemFire, Hadoop, RabbitMQ, etc...)

#### Disclaimers

* Refer to the official [Cloud Foundry documentation](http://docs.cloudfoundry.org/deploying/vsphere/hardware_spec.html) for hardware requirements. Our intent is primarily to describe what we've seen in day-to-day usage.

* These are not production deployments. They typically are built and torn down fairly quickly and are only there to run tests. They are fairly minimal and are *representative of development deployments only*.

* Our production instance. [Pivotal Web Services](https://run.pivotal.io/), is much larger than any environment described here.

#### Background

We are on a team that maintains the Cloud Foundry development environments that run on VMware vSphere. We wrote a [script](https://gist.github.com/cheeseblubber/5125054e427b0828a45c) which breaks down the disk/RAM/CPU usage of each Cloud Foundry deployment (each deployment runs in its own resource pool). We ran our script against a dozen of the development environments and determined the following hardware usage:

```
Resource Pool Disk (GB) RAM (GiB)  VMs
crane               466        46   27
poodle              595        54   28
gopher              597        54   29
hamster             614        67   33
leopard             623        58   32
lobster             628        60   36
giraffe             639        60   34
donkey              682        70   36
fox                 737        61   30
badger              740        61   30
duck                790        72   31
croc                807        88   43
lion                870       117   68
koala               901       222   60
eagle               912       103   54
camel               941        87   45
bonobo             1039       143   70
bear               1167        86   56
deer               1235        65   43

Average             789        83   41
Std Deviation       206.0      41.6 13.8
```

The spreadsheet detailing the information can be seen [here](https://docs.google.com/spreadsheets/d/15_F-eL2vrz27NAHhwb8jN4K5x8lmA7Rh-aHTNOpeY5g/edit?usp=sharing).

### RAM
Cloud Foundry can be deployed as little as 46GiB of RAM; however, the average deployment size is 83GiB with standard deviation of 41GiB. Upshot: 64GiB is good for small deployments, 128GiB would handle all but two of our deployments.

### CPU
We need but two physical cores. Cloud Foundry a surprisingly low CPU burden (one member of my team deployed Cloud Foundry on an environment with only 2 cores!).

We *do* need more than one core because vSphere 5.5’s Virtual CPU limit is [32 Virtual CPUs per core](http://www.vmware.com/pdf/vsphere5/r55/vsphere-55-configuration-maximums.pdf), and Cloud Foundry deployments require more than 32 virtual CPUs.

Unfortunately the RAM requirements exceed Intel's Haswell ceiling (32GiB), which means that Cloud Foundry deployments are typically done on Xeons (but not the 12xx line, which, like the Haswells, has a 32GiB limit).

Intel has an interesting low-end chipset, the [Avoton C2750](http://ark.intel.com/products/77987), an eight-core chip which can address 64GiB of RAM. We would be curious to hear of the experience of anyone who installs Cloud Foundry on an Avoton-based system.

### Disk
The smallest deployment in our development environment uses 466GB of hard disk space. Most of our deployments use less than 1TB of hard disk storage.

The average deployment uses 789GB of hard disk space with a relatively tight standard deviation of 206GB.
