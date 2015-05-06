---
layout: post
title: "Installing Cassandra on CentOS - Simple Way"
description: "In this post we present bash script for easy Apache Cassandra database installation in multinode CentOS enviroment"
category: [Utilities]
tags: [Bash, Apache Cassandra]
---

Installing Cassandra on CentOS is a relatively straightforward task. Nevertheless, DataStax's procedure for installing database from RPM is not the easiest one.
Script provided here is designed to automate Cassandra setup process, with ability to install additional tools, such as DataStax OpsCenter and Datastax Node Agents, for simplifying installing Cassanda on multiple node setups.

<!--more-->

**Script can be used as follows:**


`install_cassandra.sh -i "node_ip1, node_ip2" [-o] [-c] [-a opscenter_ip]`

where

`-i "node_ip1, node_ip2"` - Cassandra Node IPs (IPs of nodes where you install Cassandra, used for node-to-node communication)

`-o` - Install DataStax OpsCenter switch. You need only one DataStax OpsCenter installation

`-c` - Clear old Cassandra data

`-a opscenter_ip` - IP of node where DataStax OpsCenter is installed. It is also needed on node where you are installing DataStax OpsCenter.

Script code can be found below. If you have any problems with script, don't hesitate to mail me at antoni.sobkowicz @ opi.org.pl

``` bash
#!/bin/bash
# Make sure only root can run our script
if [[ $EUID -ne 0 ]]; then
   echo "This script must be run as root" 1>&2
   exit 1
fi
# Get options
while getopts i:ocha:l: option
  do case "${option}" in
    i) cassandra_ips=${OPTARG};;
    o) install_ops=true;;
    c) clear_data=true;;
    h) usage=true;;
    a) install_ops_ip=${OPTARG};;
  esac done

# Usage info
if [[ $usage && ${usage-_} ]]
  then
  echo "Cassandra installation script for CentOS 6.5" 1>&2
    echo Usage: cassandra_setup.sh -i \"node1_ip,node2_ip\" [-o] [-c] 1>&2
    echo "-i - Node IPs"
    echo "-o - Install OpsCenter web UI"
    echo "-c - Clear old data, if any"
  exit 1
fi


if [[ $cassandra_ips && ${cassandra_ips-_} ]]
  then
  #Checking if datastax repo exists, if not - add it to yum repos
  if [ ! -f /etc/yum.repos.d/datastax.repo ];
    then
    echo "Adding DataStax repo..."
    echo "[datastax]
name = DataStax Repo for Apache Cassandra
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0" >  /etc/yum.repos.d/datastax.repo

  fi
  echo "Installing Cassandra and tools..."
  yum -q -y install dsc21             # Installs Cassandra
  yum -q -y install cassandra21-tools # Installs optional utilities.
  if [[ $install_ops && ${install_ops-_} ]]
    then
    echo "Installing OpsCenter..."
    yum -q -y install opscenter
    cp -f /etc/opscenter/opscenterd.conf /etc/opscenter/opscenterd.conf.bak
    perl -pi -e "s/interface = 0.0.0.0/interface = $install_ops_ip/g" /etc/opscenter/opscenterd.conf
  fi

  if [[ $install_ops_ip && ${install_ops_ip-_} ]]
    then
    echo "Installing DataStax Agent..."
    yum -q -y install datastax-agent
    echo "Setting DataStax Agent configuration,OpsCenter instlled on $install_ops_ip..."
    echo "stomp_interface: $install_ops_ip" | tee -a /var/lib/datastax-agent/conf/address.yaml
  fi

  # Stop Cassandra and delete old data, if any

  echo "Stopping Cassandra service..."
  service cassandra stop
  if [[ $clear_data && ${clear_data-_} ]]
    then
    echo "Clearing old data..."
    rm -rf /var/lib/cassandra/data/system/*
  fi

  # Setting up node information
  echo "Setting up node information..."
    cp -f /etc/cassandra/conf/cassandra.yaml /etc/cassandra/conf/cassandra.yaml.bak
    perl -pi -e "s/listen_address: localhost/listen_address: /g" /etc/cassandra/conf/cassandra.yaml
    perl -pi -e "s/rpc_address: localhost/rpc_address: /g" /etc/cassandra/conf/cassandra.yaml
    perl -pi -e "s/- seeds: \"127.0.0.1\"/- seeds: $cassandra_ips/g" /etc/cassandra/conf/cassandra.yaml
#    perl -pi -e 's/abc/XYZ/g' /tmp/file.txt


  if [[ $install_ops_ip && ${install_ops_ip-_} ]]
    then
    echo "Starting DataStax Agent service..."
    service datastax-agent stop
    service datastax-agent start
    service opscenterd stop
    service opscenterd start
  fi


    # Starting cassandra
    echo "Starting Cassandra service..."
    service cassandra start
else
	echo "Cassandra nodes IP variable not set. Use -i to set variable. -h for more information"
fi

```
