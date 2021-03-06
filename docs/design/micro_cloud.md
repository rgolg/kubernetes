# Kubernetes Micro Cloud Node (MCN)
This is a proposal for Kubernetes to support a single node model, a Micro Cloud Node (MCN). A farm of MCNs is used to scale the Kubernetes cloud beyond a single MCN. In this configuration, the master, as well as the minion, reside on the same node. Weighted pods are distributed into MCNs in which the master communicates with a locally running Kubelet to manage pods within the minion running on the same node.

All Kubernetes cluster management network traffic is kept inside the MCN, keeping cluster management network chatter all internal, reducing additional complexities and security considerations when managing minions remotely.

Pods are weighted: A weight is assigned to each type of pod signifying the pods resource utilization requirements. The weight factor quantifies the resource utilization requirement for each pod. Weighted pods allow for simple scheduling and distribution of pods within an MCN and across multiple MCNs (MCN Farm), reducing the need for complex monitoring and simplifying auto-scaling decisions. 

This approach also simplifies scaling out micro cloud nodes when additional resources are needed for running more pods. Given the size of a Micro Cloud Node (RAM, CPU, etc.), a total node capacity is calculated. An MCN's total capacity is the number of pod units which can "fit" into the MCN. A threshold is defined for the maximum number of pod units deployed into a MCN. Once the threshold is reached, the replication controller running in the local master triggers the creation of a new MCN which is automatically added to the MCN farm's load-balancer.

The api server running in the master within each MCN is attached to a load-balancer when the MCN is deployed. Implementation of the interactions with a load-balancer would benefit from a pluggable model. A load-balancer plugin would simplify using different cloud providers' existing load-balancing mechanisms for load-balancing between MCNs, as well as load-balancing pod proxies across individual MCNs within an MCN farm.

Scale down of MCNs is triggered by an internal MCN capacity monitors which counts the total number of running pods within the node and the total capacity available across all MCNs within an MCN farm. In addition to the internal capacity monitors which trigger the scaling of MCNs within an MCN farm, all automated scaling can also be triggered by external synthetic monitors which monitor the performance of an application deployed to the MCN farm, from a application user's point of view.

