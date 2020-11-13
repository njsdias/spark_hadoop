# HDFS Quick Preview

- Properties files
  - /etc/hadoop/conf/core-site.xml
  - /etc/hadoop/conf/hdfs-site.xml

- Important Properties
  - fs.defaultFS
  - dfs.blocksize
  - dfs.replication

- HDFS commands
  - Copying files
    - From local file system (hadoop fs -copyFromLocal or -put)
    - Tolocal filesystem (hadoop fs-copyToLocal or -get)
    - From one HDFS location to other (hadoop fs-cp)
  - Listing files(hadoop fs -ls
  - Previewing data from files (hadoop fs -tail or -cat)
  - Checking sizes of the files (hadoop fs -du)


Inside of Hadoop cluster we can access the proprieties files that controls the
environment, HDFS, Yarn, etc. You cna see thse files typing:

    ls -ltr

- core-site.xml: we can see the value of *fs.defaultFS* that give us the location
where the main process runs in cluster.

    https://name:50070 : to enter in the node to get the cluster details

- hdfs-site.xml:  we can see the value of *dfs.blocksize* and *dfs.replication*

- Understand the different between local files and HDFS system.


To copy files from and to HDFS we have a interface command:

    hadoop fs

To see the files inside of your user account in cluster type:

    hadoop fs -ls /user/username

If you are in the Sandbox and you if you don't have user space for the root you
need to create it and change the permissions:

    sudo -u hdfs hadoop fs -mkdir /user/root;
    sudo -u hdfs hadoop fs -chown -R root /user/root

To **copy files from local files to HDFS**

    hadoop fs -copyFromLocal /local_path/foldername /user/username_in_hdfs/.

The dot at the end of the command is necessary to guarantee all folders
will be created.

To verify the files:

    // to see the all files of user
    hadoop fs -ls /user/username

    // to see the files that was copied
    hadoop fs -ls -R /user/user_name_in_hdfs/foldername

    // to see the size of the folder copied
    hadoop fs -du -s -h /user/sername_in_hdfs/foldername

    // to see how the foldername is stored in hfds and the blocks that it was divided
    hdfs fsck /user/unsername_in_hdfs/foldername -files -blocks -locations

By the last command we can see a list of blocks and the information for each block:

- blockID:  blk_id
- size: len=number_in_bytes
- replication: repl=3 (the number of nodes that a block was stored)
- IP of each node: DatanodeInfoWithStorage[255.255.25:port_number, DS-id-DISK]
