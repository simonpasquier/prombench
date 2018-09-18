# Prombench

The proposal for Prometheus CI project can be found [here](https://docs.google.com/document/d/1aCGHS0hOrh3LiQLuOa1EWA6knF7HmqWbhp3ev66hB7Y/edit?ouid=118160464041419930165&usp=docs_home&ths=true)
 & a detailed summary can be found [here](https://docs.google.com/document/d/1rR6-UsqJ9g5LbH7lnjVmWyNTGPAGzbytricbVCJNqWg/edit).

- Assume [Prow](https://github.com/prometheus/test-infra/tree/master/prow/) is already running on a GKE cluster.<br/>Instructions on how to deploy a prow-cluster can be found [here](README.md#prow-setup).<br/>The benchmark plugin added to Prow can be found [here](https://github.com/prometheus/test-infra/tree/master/prow/plugins/benchmark).

- When Prombench is triggered using `/benchmark pr` or `/benchmark release [version_number(ex:2.3.0-rc.1)|Default:latest]`, the following environment variables are set in the Prowjob [start-benchmark](components/prow/cluster.yaml):
	- ZONE: Zone of the Prow cluster
	- PROJECT_ID: Project ID of the Prow cluster
	- CLUSTER_NAME: Name of the Prow cluster
	- PR_NUMBER: Number of the PR where this comment was written

- In case of `/benchmark pr`, initially a Docker image is made for the PR.

- 2 new [nodepools](components/prombench/nodepools.yaml) are created in the Prow cluster :: `prometheus-<PR_NUMBER>` and `nodes-<PR_NUMBER>`.

- [Prombench](components/prombench/manifests/benchmark) is deployed on these nodepools in a new namespace `prombench-<PR_NUMBER>` (Only one Prombench instance can run on a PR).

- When `/benchmark cancel` is triggered, the nodepools and namespace are deleted.
