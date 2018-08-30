# Hadoop Installation and Configuration

1. [Installation](#)
2. [Configuration](#)
    * [Modify XML Files](#)
    * [Configure SSH](#)
    * [Start & Stop Daemons](#)

Ref: [Hadoop Installation on Mac OS X](https://isaacchanghau.github.io/post/install_hadoop_mac/)

## 1. Installation

First make sure we have java installed.
```bash
# Check Java Version
java -version
```

Go to Hadoop release page [http://hadoop.apache.org/releases.html](http://hadoop.apache.org/releases.html) and download the newest release:

![01](images/01-01.png "01")

![02](images/01-02.png "02")

```bash
# Extract hadoop gz file
cd ~/Downloads/
tar xzf hadoop-3.1.1.tar.gz

# Move to another dir
mkdir ~/Documents/Hadoop
mv hadoop-3.1.1 ~/Documents/Hadoop/hadoop-3.1.1

# Set Java & Hadoop location in .bash_profile
vi ~/.bash_profile
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_144.jdk/Contents/Home/
export HADOOP_HOME=~/Documents/Hadoop/hadoop-3.1.1
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
# Restart terminal
echo $JAVA_HOME # Make sure we have the new java location
echo $HADOOP_HOME

# Check Hadoop runs
hadoop version # Hadoop 3.1.1
```

## 2. Configuration

Each component in Hadoop is configured using an XML file. They are all in the subdirectory `~/Documents/Hadoop/hadoop-3.1.1/etc/hadoop/`. Hadoop can be run in one of three modes:
* Standalone Mode
* Pseudodistributed Mode
* Fully Distributed Mode

Only consider the configuration for the second mode here (nothing to do for 1st mode, too much to consider for 3rd mode).

### 2.1. Modify XML Files

Create a backup folder for the configuration files, in case we mess them up in the future:
```bash
cd /Users/liyuq/Documents/Hadoop/hadoop-3.1.1/etc
cp -r hadoop hadoop_backup
```

Set env variable `HADOOP_CONF_DIR`:

```bash
# Modify ~/.bash_profile
vi ~/.bash_profile
export HADOOP_CONF_DIR=~/Documents/Hadoop/hadoop-3.1.1/etc/hadoop/

# Check env variable created
source ~/.bash_profile
echo $HADOOP_CONF_DIR # /Users/.../Documents/Hadoop/hadoop-3.1.1/etc/hadoop、
```

Open the following xml files in the dir `~/Documents/Hadoop/hadoop-3.1.1/etc/hadoop/` and add some `property` tags in the `configuration` tag:

* **core-site.xml:**
    ```xml
    <configuration>
        <property>
            <name>fs.defaultFS</name>
            <value>hdfs://localhost/</value>
        </property>
    </configuration>
    ```
* **hdfs-site.xml:**
    ```xml
    <configuration>
        <property>
            <name>dfs.replication</name>
            <value>1</value>
        </property>
    </configuration>
    ```
* **mapred-site.xml:**
    ```xml
    <configuration>
        <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
        </property>
    </configuration>
    ```
* **yarn-site.xml:**
    ```xml
    <configuration>
        <property>
            <name>yarn.resourcemanager.hostname</name>
            <value>localhost</value>
        </property>
        <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
        </property>
    </configuration>
    ```

### 2.2. Configure SSH

Hadoop will starts daemons on the set of hosts by SSHing to each host and starting a daemon process. So we need to make sure we can SSH to localhost without a password.

Make sure the remote login is checked in your Mac:

![03](images/01-03.png "03")

Generate a new SSH key with an empty passphrase to enable passwordless login:
```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Then test that you can SSH into localhost with:
```bash
ssh localhost
# Use 'control + D' to exit localhost
```

If successfully, you should not have to type in a password. Then format the HDFS file system before the first use:
```bash
hdfs namenode -format
```

### 2.3. Start & Stop Daemons

Since we placed configuration files in a different dir (`~/Documents/Hadoop/hadoop-3.1.1/etc/hadoop/`), we need to start the daemons with the `--config` option.

The scripts are under `~/Documents/Hadoop/hadoop-3.1.1/sbin/`, which is already defined in our `~/.bash_profile`.



```bash
# Start services
start-dfs.sh # start HDFS service
start-yarn.sh
mr-jobhistory-daemon.sh start historyserver

# Stop services
mr-jobhistory-daemon.sh stop historyserver
stop-yarn.sh
stop-dfs.sh # stop HDFS service
```

Output for `start-dfs.sh` should be something like:
```
Starting namenodes on [localhost]
Starting datanodes
Starting secondary namenodes [...xxx...]
8c8590431d73.ant.amazon.com: ssh: connect to host ...xxx... port 22: Operation timed out
2018-08-30 15:23:16,711 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

It means the local service launched successfully, then open Resource Manager in browser through the link http://localhost:50070 (**changed to port 9870 for Hadoop 3!**, ref: https://stackoverflow.com/questions/19641326/http-localhost50070-does-not-work-hadoop), you can see the following page:

![04](images/01-04.png "04")
