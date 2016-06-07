# docker-jenkins

[![Build Status](https://travis-ci.org/h0tbird/docker-jenkins.svg?branch=master)](https://travis-ci.org/h0tbird/docker-jenkins)

This is a Mesos-aware containerized jenkins server.

- It will run an eager Jenkins master (more details below).
- It will provision slaves in a Mesos cluster.
- The Mesos cluster is discovered using a Zookeeper cluster.
- It will register as a Mesos framework only if the job queue has jobs.

```
docker run -it --rm \
--env JENKINS_HTTPPORT=8282 \
--env JENKINS_AJP13PORT=-1 \
--env JENKINS_SYSTEM_MESSAGE=jenkins-qa \
--env JENKINS_NODE_PROVISIONER_MARGIN=50 \
--env JENKINS_NODE_PROVISIONER_MARGIN0=0.85 \
--env JENKINS_ADMIN_EMAIL=nobody@nowhere \
--env JENKINS_DOMAIN=marathon \
--env MESOS_MASTER=zk://master-1:2181,master-2:2181,master-3:2181/mesos \
--env MESOS_FRAMEWORK_NAME=jenkins-framework \
--env MESOS_CHECKPOINT=true \
--env MESOS_ON_DEMAND_REGISTRATION=true \
--env MESOS_IDLE_TERMINATION_MINUTES=1 \
--env SSL_TRUST=foo:443,bar:443 \
--name jenkins \
--volume ${PWD}/jenkins_data:/var/lib/jenkins \
--publish 8080:8282 \
h0tbird/jenkins:v2.8-1
```

### Over provisioning flags

By default, Jenkins spawns slaves conservatively. Say, if there are 2 builds in queue, it won't spawn 2 executors immediately. It will spawn one executor and wait for sometime for the first executor to be freed before deciding to spawn the second executor. Jenkins makes sure every executor it spawns is utilized to the maximum.
If you want to override this behvaiour and spawn an executor for each build in queue immediately without waiting, you can use these flags during Jenkins startup:
`JENKINS_NODE_PROVISIONER_MARGIN=50` `JENKINS_NODE_PROVISIONER_MARGIN0=0.85`
