# To create EFK on kubernetes with kibana user credentials:

password protected EFK refer: have to do some changes in fluentd rbac.yaml file , in the given folder the changes are done. look at docker image
https://www.deepnetwork.com/blog/2020/03/13/password-protected-efk-stack-on-k8s.html

efk require 4 cpu and 8 gb ram> in above script namespace is not specified in yaml file, we have to set namespace then create resources

1) follow the document and first go inside folder and delpoy elasticsearch
$ kubectl apply -f .

2) run the following command to generate password and save it somewhere
$ kubectl exec -it elasticsearch-0 -n kube-logging -- bin/elasticsearch-setup-passwords auto -b
we get following stuff:
______________________________________
Changed password for user apm_system
PASSWORD apm_system = hDWUb0P1sr0MLidSFbyB

Changed password for user kibana
PASSWORD kibana = YvL6Xdjl7UockbwYg4ff

Changed password for user logstash_system
PASSWORD logstash_system = U8VoZ2DUJ1Y94RNYn4E3

Changed password for user beats_system
PASSWORD beats_system = w9Ogh8MILAVJYeLymiiG

Changed password for user remote_monitoring_user
PASSWORD remote_monitoring_user = BPV9OvGC0HBw2xVCatAj

Changed password for user elastic
PASSWORD elastic = iXcCJJG3g47JhNcOVsqs
_______________________________________

3) now go to kibana folder and in configmap.yaml file and change the password to > "PASSWORD elastic" as we get above, then deploy kibana
$ kubectl apply -f .

>>port forwarding is possible when u access a cluster from ur local machine , not from aws cli
4) $ kubectl get svc -n kube-logging

5) now browse the ec2 instance ip:(kibana service port <5 digit port>) -the kibana dashboard will open
supply username : elastic
password: iXcCJJG3g47JhNcOVsqs

6) Now go to fluentd folder and change password in configmap.yaml and deploy fluentd
$ kubectl apply -f .

at kibana dashboard write [logstash-*] >> next "add filter+" >> @timestamp >> next
=====================================================================================
Theory::
for production we have to use secrets not configmaps

Other links:
non password refer : 
https://mherman.org/blog/logging-in-kubernetes-with-elasticsearch-Kibana-fluentd/

https://www.digitalocean.com/community/tutorials/how-to-set-up-an-elasticsearch-fluentd-and-kibana-efk-logging-stack-on-kubernetes

>>efk authentication:readme.md>  https://github.com/rsarkar29/EFK

Q) elasticsearch use as a statefulsets why?
?? Deployments and ReplicationControllers are meant for stateless usage and are rather lightweight. StatefulSets are used when state has to be persisted. Therefore the latter use volumeClaimTemplates / claims on persistent volumes to ensure they can keep the state across component restarts.

Q) What is EFK
>>EFK is a suite of tools combining Elasticsearch, Fluentd and Kibana to manage logs. Fluentd will collect the logs and send it to Elasticsearch. This latter will receive the logs and save it on its database. Kibana will fetch the logs from Elasticsearch and display it on a nice web app.

Q) where the logs stored? path?
>>

Q) how to use kibana to see logs
