# Using the Scripts - Public-Routed Network

The primary expected use case for these scripts is in a network that is able to to reach Internet-hosted resources. In such usage-contexts, the tools would typically be executed similarly to:

1. Launch an AMI to act as a build host
2. Attach a 20GiB EBS to the build host
3. Login to the build host and escalate privileges to root
4. Clone this project to the build host (make sure the destination filesystem allows script-execution)
5. Execute the following sequence:

~~~
    cd /PROJECT/CLONE/PATH ; \
      ./DiskSetup.sh -b /boot -v VolGroup00 -d /dev/xvdf ; \
      ./MkChrootTree.sh	/dev/xvdf ; \
      ./MkTabs.sh /dev/xvdf ; \
      ./ChrootBuild.sh ; \
      ./AWScliSetup.sh ; \
      ./ChrootCfg.sh ; \
      ./GrubSetup.sh /dev/xvdf ; \
      ./NetSet.sh ; \
      ./CleanChroot.sh ; \
      ./PreRelabel.sh	 ; \
      ./Umount.sh
~~~

Once the above sequence exits successfully, an AMI may be created from the target-disk (/dev/xvdf in the example above):

1. Shut down the build-host
1. Detach boot EBS
1. Detach build-EBS
1. Re-attach build-EBS to boot EBS's original location
1. Register an AMI from the stopped instance
1. Launch a test-instance from the newly-created AMI and verify that it functions as expected.

The OS-specific components can be further automated by using frameworks like [Packer](https://www.packer.io/). One such project that does this is Plus3 IT's [spel](https://github.com/rbdunne/spel) project.
