# ds-for-telco

This repo is a sample Jupyter Lab notebook with supporting data that helps demonstrate D2iQ's DC/OS Data Science Engine. It is a modified version of the http://github.com/gregoryg/ds-for-telco repo. 
 
Modifications include updating the Customer_Churn_Analysis.ipynb Jupyter notebook to use the default HDFS configuration from a DC/OS Mesos based HDFS service and to use a "data_lake" prefix in the HDFS file paths. Additionally, the following demo script and demo setup details are included.

## I. DEMO SCRIPT - DC/OS Data Science Engine (DSE) 

1. Present a few slides on D2iQ's DC/OS Data Science Engine with the value propositions:

     - DC/OS Data Science Engine accelerates ML with dramatically lower costs

     - DC/OS Data Science Engine enables rapid on-boarding

     - DC/OS Data Science Engine's Secure collaboration increases experiment velocity

     - DC/OS Data Science Engine's Elastic resource pooling accelerates model training

     - DC/OS Data Science Engine scales and accelerates data science at much lower cost

     - DC/OS Data Science Engine accelerates and simplifies Machine Learning workflow

2. Present an overview of Mesosphere DC/OS for data analytics workloads and show/describe the following:

     - DC/OS Cluster Management - Nodes, Resources, On-prem, Cloud & Regions, AZs
     - Jupyter Lab
     - Spark
     - TensorFlow
     - GPU support
     - Hive Metastore
     - Zeppelin Notebook
     - Apache HDFS
     - Apache Kafka
     - Apache Flink - stream processing
     - Apache NiFi - data flows
     - Apache Storm
     - Elastic

3. Show how easy it is to install a highly available HDFS service in DC/OS and discuss the following:

     - Persistant storage support
     - Kerberos integration
     - HA enabled
     - TLS enabled
     - DC/OS Secret support
     - Deployment Plans

4. Show how Mesosphere Data Science Engine (DSE) can easily be setup for multiple teams and team members. Show and discuss the following:
     
     - Support for multiple groups and users

          ```
          /team1/dev/dse-ringo
          /team1/dev/dse-john
          /team2/dev/dse-paul
          /team2/dev/dse-george
          ```
     - HDFS (data lake) integration

          ```Use default endpoint: http://api.hdfs.marathon.l4lb.thisdcos.directory/v1/endpoints```

     - GPU support
     - Inbound Load Balancer support and Admin Router support
     - TLS support
     - Kerberos support
     - Persistent storage support
     - User authentication with OpenID Connect
     - Distrubuted Spark support
     - Dynamic downloading of git repos
          ``` Add "git clone https://github.com/gregpalmr/ds-for-telco && " to begining of CMD```

5. Show the ds-for-telco Jupyter lab/Spark notebook

     Follow the script for the original ds-for-telco Jupyter lab & spark demo

6. Summarize what was demo'd and link it to the previously presented value propositions:

     - DC/OS Data Science Engine accelerates ML with dramatically lower costs

     - DC/OS Data Science Engine enables rapid on-boarding

     - DC/OS Data Science Engine's secure collaboration increases experiment velocity

     - DC/OS Data Science Engine's elastic resource pooling accelerates model training

     - DC/OS Data Science Engine scales and accelerates data science at much lower cost

     - DC/OS Data Science Engine accelerates and simplifies Machine Learning workflow



## II. DEMO SETUP

Step 1. Launch a DC/OS 2.x cluster
     - 1 public agents
     - 10 private agents
     - 1 or 3 master nodes
     - Use the DC/OS Terraform templates or the advanced installer


Step 2. Install DC/OS CLI (latest)

     - Follow the instructions in the pull down menu option:
         Install CLI

Step 3. Install various CLI extensions. Run the commantd:

     dcos package install --cli data-science-engine --yes

     dcos package install --cli hdfs --yes

     dcos package install --cli spark --yes


Step 4. Create some non-admin users and groups with limited access permissions.

     - Use the DC/OS Dashboard UI or use the following CLI commands:

     Group: team1

          dcos security org groups create team1

          dcos security org users create --password changeme ringo

          dcos security org groups add_user team1 ringo

          dcos security org users create --password changeme john

          dcos security org groups add_user team1 john

       Add some privileges to the group

          dcos security org groups grant team1 dcos:adminrouter:ops:mesos full

          dcos security org groups grant team1 dcos:adminrouter:ops:slave full

          dcos security org groups grant team1 dcos:adminrouter:package full

          dcos security org groups grant team1 dcos:adminrouter:service:marathon full

          dcos security org groups grant team1 dcos:secrets:default:/team1 full

          dcos security org groups grant team1 dcos:secrets:list:default:/team1 full

       Add some privileges to the users

          dcos security org users grant ringo dcos:adminrouter:service:team1/dev/dse-ringo full

          dcos security org users grant ringo dcos:service:marathon:marathon:services:/team1/dev/dse-ringo full

          dcos security org users grant john dcos:adminrouter:service:team1/dev/dse-john full

          dcos security org users grant john dcos:service:marathon:marathon:services:/team1/dev/dse-john full


     Group: team2

          dcos security org groups create team2

          dcos security org users create --password changeme paul

          dcos security org groups add_user team2 paul

          dcos security org users create --password changeme george

          dcos security org groups add_user team2 george

          Also add john to this group (he is in both groups)

          dcos security org groups add_user team2 john

       Add some privileges to the group

          dcos security org groups grant team2 dcos:adminrouter:ops:mesos full

          dcos security org groups grant team2 dcos:adminrouter:ops:slave full

          dcos security org groups grant team2 dcos:adminrouter:package full

          dcos security org groups grant team2 dcos:adminrouter:service:marathon full

          dcos security org groups grant team2 dcos:secrets:default:/team2 full

          dcos security org groups grant team2 dcos:secrets:list:default:/team2 full

       Add some privileges to the users

          dcos security org users grant paul dcos:adminrouter:service:team2/dev/dse-paul full

          dcos security org users grant paul dcos:service:marathon:marathon:services:/team2/dev/dse-paul full

          dcos security org users grant george dcos:adminrouter:service:team2/dev/dse-george full

          dcos security org users grant george dcos:service:marathon:marathon:services:/team2/dev/dse-george full

          dcos security org users grant john dcos:adminrouter:service:team2/dev/dse-john full

          dcos security org users grant john dcos:service:marathon:marathon:services:/team2/dev/dse-john full


Step 5. Create a demo file-based secrets holding a fake kerberos keytab file contents

     echo "KEYTAB TEAM1 DEV FILE CONTENTS " >  kerberos-team1-dev.keytab
     dcos security secrets create --file kerberos-team1-dev.keytab /team1/dev/dev_kerberos_keytab

     echo "KEYTAB TEAM1 TEST FILE CONTENTS " >  kerberos-team1-test.keytab
     dcos security secrets create --file kerberos-team1-test.keytab /team1/test/test_kerberos_keytab

     echo "KEYTAB TEAM2 DEV FILE CONTENTS " >  kerberos-team2-dev.keytab
     dcos security secrets create --file kerberos-team2-dev.keytab /team2/dev/dev_kerberos_keytab

     echo "KEYTAB TEAM2 TEST FILE CONTENTS " >  kerberos-team2-test.keytab
     dcos security secrets create --file kerberos-team2-test.keytab /team2/test/test_kerberos_keytab


<b>Related Content</b>:<br>

http://github.com/gregoryg/ds-for-telco
<br>
http://blog.cloudera.com/blog/2016/02/how-to-predict-telco-churn-with-apache-spark-mllib/

