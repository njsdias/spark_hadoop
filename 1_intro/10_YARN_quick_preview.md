# YARN Quick Preview
Yarn: distributed process framework

In certifications Spark typically runs in YARN mode.

We should be able to check the memory configuration to understand the cluster
capacity:

  - /etc/hadoop/conf/yarn-site.xml
  - /etc/spark/conf/spark-env.sh

Spark default settings
  - number of executors: 2
  - memory: 1 GB

Quite often we under utilize resources. Understanding memory settings thoroughly
and then mapping them with data size we are trying to process we can
accelerate the execution of our jobs.


- yarn-site.xml: search for

  - *resourcemanager.address* to see the address and the port number
  - *resorucemanager.webapp.address* to see the address and the Port number for
    the web application. If you put the address and the port number in the
    web browser you will see the *Memory Total* and *VCores Total*. These are the settings
    that tells us the YARN memory on the cluster. In left menu on *Nodes* option
    it allows to see the details of the Nodes (names, ports and memory). Adding
    memory used and memory available we get the *Memory Total*. If we address
    *VCores Used* and *VCores Available* we get the *VCores Total*

**Important:** By default each Spark job takes 2GB and 2 Cores. It is
controlled by proprieties that are in spark-env.sh
