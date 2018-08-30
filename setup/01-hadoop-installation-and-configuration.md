# Hadoop Installation and Configuration

1. [Installation](#)
2. [Configuration](#)

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

Only consider the configuration for the second mode here (nothing to do for 1st mode, too much to consider for 3rd mode):

Copy all configuration files (`*-site.xml`) to a new directory and set env variable `HADOOP_CONF_DIR`:

```bash
# Move xml configure files to a different directory
cd ~/Documents/Hadoop/hadoop-3.1.1/etc/
mkdir hadoop_conf
cp hadoop/*-site.xml hadoop_conf/

# Modify ~/.bash_profile
vi ~/.bash_profile
export HADOOP_CONF_DIR=~/Documents/Hadoop/hadoop-3.1.1/etc/hadoop_conf

# Check env variable created
source ~/.bash_profile
echo $HADOOP_CONF_DIR # /Users/.../Documents/Hadoop/hadoop-3.1.1/etc/hadoop_conf

```
