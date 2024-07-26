# WSD-DevOps-Challenge

Questions:

Configuration management:

1)  Which ansible command can display all ansible_ configuration for a host.<br>
    The command is "ansible < host > -m setup"
    
3) Please configure a cron job that runs logrotate on all machines every 10 minutes between 2h - 4h.<br>
   Answer: Using ansible to configure a cronjob [logrotate cronjob](https://github.com/Joaofranciscopanta/WSD-DevOps-Challenge/blob/main/Configuration%20Management/logrotate_cron.yml)
   
4) Deploy an ntpd package on 3 servers, then, deploy nagios template on the 3 servers and another machine:<br>
   To deploy the NTPD package: [Deploy ntpd](https://github.com/Joaofranciscopanta/WSD-DevOps-Challenge/blob/main/Configuration%20Management/deploy_ntp.yml)
   To deploy the Nagios template: [Deploy nagios](https://github.com/Joaofranciscopanta/WSD-DevOps-Challenge/blob/main/Configuration%20Management/deploy_nagios.yml)
   Also consider the usage of hosts.ini


Kubernetes:

1) Prepare a docker-compose for a nginx server with log permanence and network bridge subnet 172.20.8.0/24:<br>
   Answer: [Docker-compose](https://github.com/Joaofranciscopanta/WSD-DevOps-Challenge/blob/main/Kubernetes/docker-compose.yml)

2) Which Kubernetes command you will use to identify the reason for a pod restart in the project &quot;internal&quot; under namespace &quot;production&quot;.<br>
   Identify with "kubectl get pods -n production" then "kubectl describe pod <pod_name> -n production" to inspect pod.
   It’s also possible to grep the output and filter some keywords to skip the whole pod description:

      kubectl describe pod java-app-7d9d44ccbf-lmvbc -n production | grep -iE "killing|unhealthy|crashloopbackoff|oomkilled"

      kubectl describe pod java-app-7d9d44ccbf-lmvbc -n production | grep -i "restart count"

      kubectl describe pod java-app-7d9d44ccbf-lmvbc -n production | grep -iE "error|fail|exception"

3) Java-app keep restarting at random. From Kubernetes configuration perspective, what are the possible reasons for the pod restarts?<br>
   Badly configured probes(liveness & readiness) might be causing the issue and cannot be disregarded, however, it might also be OOM errors related to the xmx limit setting or even memory limit, considering the possibilities of spikes in resource consumption.


Helm <br>
Answers: Had to switch images for the deploy to work, mostly an error related to internal/private or even deprecated images.<br>
Deployment.yml file: [Deployment.yaml](https://github.com/Joaofranciscopanta/WSD-DevOps-Challenge/blob/main/Helm/deployment.yaml)<br>
Deployment using both, statefulset and deployment file and usge of longhorn storageclass: ![image](https://github.com/user-attachments/assets/7af65050-b0d2-4282-bc4f-1101be8e6adb)
![image](https://github.com/user-attachments/assets/ae3f7a00-5f4c-43f1-ade8-dbcd850be0b3)

Prometheus:
1) Explain how Prometheus work.<br>
  Prometheus works as a monitoring tool by scraping data from http endpoints, gathering metrics and data and pushing in a time-based database.

2) How do you create custom Prometheus alerts and alerting rules for Kubernetes monitoring? Provide an example alert rule and its configuration.<br>
  Custom rules can be created using yml files, example: Alert for memory consumption above 90%: [Custom alert](https://github.com/Joaofranciscopanta/WSD-DevOps-Challenge/blob/main/Prometheus/examplealert.yml)

3) What is the Prometheus query you can use in Granfana to properly show usage trend of an application metric that is a counter?<br>
   A rate() is a query that can be used in grafana and show usage trend of counters, example: rate(http_requests_total[5m])
   
Databases
1) Query to db cluster returns different result each time. Users reported query result has data records that they deleted days ago. Explain what the likely reason for the behavior and how to avoid it.<br>
   It may be a problem related to consistency, it takes some time to make every node consistent, this might be caused by tombstone usage.
   It might also be due to the querys not using strong consistency filters.

2) Please provide all steps required to shard the collection sanfrancisco.company_namebased on _id.<br>
Steps:
  1) Ensure both replicaset_1 and replicaset_2 are set up and running properly.
  2) Add shards to the cluster with sh.addShard("replicaset_1/<replicaset_1_primary>:<port>") and sh.addShard("replicaset_2/<replicaset_2_primary>:<port>")
  3) Enable sharding with sh.enableSharding("sanfrancisco")
  4) Index the shard key with "use sanfrancisco" then "db.company_name.createIndex()"
  5) Then shard the collection: sh.shardCollection("sanfrancisco.company_name", { "_id": 1 })



