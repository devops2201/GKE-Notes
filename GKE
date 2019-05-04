-> GKE is a managed, production ready environment for deploying containerized applications

-> GKE isnt just for stateless applications, you can attach persistent storage and even run a database in your cluster

-> Describe compute, memory and storage resources your application containers require; GKE provisions and manages underlying cloud resources automatically

-> Scale to meet demand (single machine to thousands)

-> Runs securely on VPC

GKE Features
************
1. IAM
2. Hybrid networking
3. Integrated logging and monitoring (Stackdriver)
4. Auto scale (based on resource utilization)
5. Auto repair (Node repair if it fails health check)
6. Specify resource limits for each container needs
7. Stateful Application support
8. Fully managed
9. Runs container-optimized OS (hardened OS built and managed by Google)
10. Private Container registry
11. Fast consistent builds using Google cloud build
12. Built in dashboard


GKE quickstart
**************
1.	Create or select project
2.	Wait for API and related services to be enabled
3.	Billing should be enabled
4.	Choose either google cloud shell or local shell (cloud shell has gcloud and kubectl preinstalled)
5.	If local shell is your preference install gcloud SDK and kubectl
6.	Configure default settings (default project and compute zone) for gcloud
7.	Can specify --project --zone and --cluster to gcloud commands

gcloud commands
***************
gcloud config set project <project_id>

gcloud config set compute/zone <compute_zone>

Creating GKE cluster
********************
gcloud container create <cluster_name>

To authenticate for the cluster
*******************************
gcloud container clusters get-credentials <cluster_name>

Create a deployment
*******************
kubectl run hello-server --image gcr.io/google-samples/hello-app:1.0 --port 8080 

Exposing the Deployment
***********************
kubectl expose deployment hello-server --type LoadBalancer \
    --port 80 --target-port 8080

Inspect and view the application
********************************
kubectl get service hello-server

Delete cluster
**************
gcloud container clusters delete <cluster_name>


GKE Cluster administration
**************************
-> Basic cluster creation is handled by GCP; gcloud, GKE API, GCP Console are used to control cluster's structural configuration

-> Cluster level configuration: Creating, Upgrading, deleting GKE clusters
set up cluster level autoscaling, enable/disable logging

Node configuration
******************
-> Creating one or more node pools
(Node pools are groups of nodes within your cluster that share common configuration)

-> Cluster can consist of single(default) node pool or multiple node pools

-> Node options can be set on a per-pool basis
  •	Automatic upgrades
  •	Automatic repairs
  •	Preemtible VMs (Last only for a max of 24 hours; priced lower)
  •	Local SSDs
  •	Minimum CPU platform

-> Node auto-upgrades help you keep nodes in your cluster up to date with cluster master version when your master is updated on your behalf

-> Node auto-upgrade is enabled by default when a new cluster is created
Benefits
********
Lower management overhead (No manual efforts to track and update your nodes)
Better security (new binaries related to security are automatically applied and kept to date)
-> After master nodes are upgraded, node-pools are scheduled for upgrades
-> Nodes are drained and re-created to match current cluster master version
-> To have more control schedule a maintenance window
-> You can disable node auto-upgrades on one or more node-pools at any time

How node auto-upgrade works?
****************************
-> Only one node pool;s nodes are upgraded at a time
-> In a node pool, one node is upgraded at a time (in random order)
-> Nodes are auto-upgraded during a MW

1.	Node is cordoned and drained (Cannot run new pods)
2.	Pods on node are rescheduled onto other nodes; If it can be rescheduled it stays in PENDING state until node is recreated
3.	Node is deleted and recreated
4.	If new node fails to register as healthy, auto-upgrade of entire node pool is disabled
5.	If x no of nodes are unhealthy, upgrades to that version are halted

Creating a cluster or node pool with node auto-upgrades
*******************************************************
gcloud container clusters create <cluster_name> --enable-autoupgrade

Create a node pool with auto-upgrades
*************************************
gcloud container node-pools create <node_pool> --cluster <cluster_name> \
        --zone <compute_zone> --enable-autoupgrade

Enable node auto-upgrades for an existing node-pool
***************************************************
gcloud container node-pools update <node_pool> --cluster <cluster_name> \
        --zone <compute_zone> --enable-autoupgrade

Disable node auto-upgrades for existing node-pool
*************************************************
gcloud container node-pools update <node_pool> --cluster <cluster_name> \
        --zone <compute_zone> --no-enable-autoupgrade

Types of Clusters in GKE
************************
1.	Zonal cluster (Runs in one or more compute zones within a region; multi-zone cluster runs its nodes across two or more compute zones within a single region; Run a single cluster master)
2.	Regional cluster (Runs 3 cluster masters across 3 compute zones, runs nodes in two or more compute zones)
3.	Private cluster (Zonal or regional which hides masters and nodes from public Internet)
4.	Alpha cluster (Experimental zonal or regional cluster that runs with alpha kubernetes features enabled; Expire in 30 days)

Cluster Templates
*****************
1.	Standard (3-node zonal cluster)
2.	Your first cluster (small cluster running less powerful nodes and disables autoscaling)
3.	CPU Intensive (multi-core CPUs)
4.	Memory Intensive (multi-core CPUs + large memory )
5.	GPU Accelerated computing (less powerful nodes + GPU enabled node pool)
6.	Highly available (regional cluster; cluster masters in each zone of a given region; auto-scaling and MW are enabled)

-> Multi-zone clusters aid in HA with protection against downtime (in case of zone-wide outage)
-> node pools in multi-zone clusters automatically have multi-zone availability

Single zone cluster
*******************
gcloud container clusters create <cluster_name> --zone <compute_zone>

Multi-zone cluster
******************
gcloud container clusters create <cluster_name>  \
--zone <compute_zone> \
--node-locations <compute_zone1>,<compute_zone2> ...

Regional Cluster
****************
gcloud container clusters create <cluster_name>  --region <region> \
--node-locations <compute_zone1>,<compute_zone2>...


Auto-repairing Nodes
********************
-> Helps you keep nodes in a healthy, running state
-> When enabled, GKE makes periodic checks on health state of each node
-> If periodic health checks fail, GKE initiates a repair process

Repair criteria
***************
-> Node reporting READY status is considered health
-> NotReady status on consecutive checks over given time threshold (10 mins)
-> Node does not report any status over given time threshold
-> Node's boot disk is out of disk space for an extended time period (approx 30 mins)

Node repair process
*******************
-> Node is drained and recreated
-> Node drain can fail if node is unresponsive or is too unhealthy to process drain command
-> If multiple nodes require repair, GKE repairs them in parallel
-> An entry in its operation logs for any automated repair event
gcloud container operations list

Enable node auto-repair
***********************
-> enabled on per node-pool basis
cluster with auto-repair
gcloud container clusters create <cluster_name>  --zone <compute_zone> \
--enable-autorepair

Node-pool with auto-repair
**************************
gcloud container node-pools create <pool_name> --cluster <cluster_name> \
        --zone <compute_zone> --enable-autorepair

Exisiting node-pool update
**************************
gcloud container node-pools update <pool_name> --cluster <cluster_name> \
        --zone <compute_zone>  --enable-autorepair 

To disable: --no-enable-autorepair

Configure cluster networking
****************************
Networking features
1.	Internal TCP/UDP Load Balancing (Makes cluster's services accesible to applications outside of your cluster that use same VPC network and are located in same GCP region); annotation: cloud.google.com/load-balancer-type: "Internal" ; type: LoadBalancer
2.	Alias IP (With Alias IPs, GKE clusters can allocate IP addresses from CIDR block known to GCP; Makes cluster more scalable and allows cluster to interact with other GCP products)
      Benefits:
      -> Pod IP addresses are natively routable within GCP network
      -> networking layer can perform anti-spoofing checks to ensure that egress traffic is not sent with arbitrary source IP addresses
3.	IP masquerading (Form of network address translation used to perform many-to-one ip address translations)
       Using IP masquerading in your clusters can increase their security by preventing individual Pod IP addresses from being exposed to traffic outside link-local range (169.254.0.0/16) and additional arbitrary IP ranges 
Additionally, IP masquerading allows you to configure communication between IP ranges without masquerade, such as Pods in the 192.168.0.0/16 range interacting with networking resources in the cluster CIDR range (10.0.0.0/8) 

How ip-masq-agent works?
************************
-> ip-masq-agent is automatically enabled
-> cluster should have network policy and CIDR range is not within 10.0.0.0/8
-> ip-masq-agent configures iptables rules to handle masquerading node/Pod IP addressed when sending traffic to destinations outside node's and cluster's IP ranges
4.	Network Policy (control communication between your cluster's pods and Services)
-> Use Network policy API to create Pod-level firewall rules; These firewall rules determine which Pods and Services can access  one another inside the cluster
-> Defining NP helps enable things like defense in depth
-> NP makes it easier for your application to host data from multiple users
-> Use gcloud, GKE REST API or GCP console to enable/disable NP enforcement

gcloud container clusters create [CLUSTER_NAME] --enable-network-policy

gcloud container clusters update [CLUSTER_NAME] --update-addons=NetworkPolicy=ENABLED

gcloud container clusters update [CLUSTER_NAME] --enable-network-policy

gcloud container clusters update [CLUSTER_NAME] --no-enable-network-policy

Viewing Clusters
****************
gcloud container clusters describe [CLUSTER_NAME]
gcloud container clusters list


Set default cluster for gcloud
******************************
gcloud config set container/cluster [CLUSTER_NAME]


Generating kubeconfig?
**********************
gcloud container clusters get-credentials [CLUSTER_NAME]

Upgrading Clusters
******************
-> Cannot upgrade master and nodes at the same time
-> Google upgrades masters automatically but you can choose to upgrade manually
-> If pod takes longer than 10 mins to get deleted, GKE will forcibly trminate the Pod to shutdown and delete the node
-> New node instances run base OS image, docker daemon, kubelet, kube-proxy
-> Any data should be stored in Persistent volumes which use Persistent disks
-> You can roll back an upgrade

gcloud container node-pools rollback <pool_name> --cluster <cluster_name>


Resizing Clusters
*****************
gcloud container clusters resize <cluster_name> --node-pool <pool_name> \
--size <size>

Autoscaling a Cluster
*********************
gcloud container clusters create <cluster_name> --num-nodes 30 \
--enable-autoscaling --min-nodes 15 --max-nodes 50 --zone <compute_zone>


Adding node-pool with autoscaling
*********************************
gcloud container node-pools create <pool_name> --cluster <cluster_name> \
--enable-autoscaling --min-nodes 1 --max-nodes 5 --zone <compute_zone>


Deleting a cluster
******************
gcloud container clusters delete <cluster_name>

Adding node pool
****************
gcloud container node-pools create <pool_name> --cluster <cluster_name>

List node pools
***************
gcloud container node-pools list --cluster <cluster-name>

Describe node pool
******************
gcloud container node-pools describe <pool_name> --cluster <cluster_name> 

Resizing a node pool
********************
gcloud container clusters resize <cluster_name>  --node-pool <pool_name> --size <size>

Upgrade a node pool
*******************
gcloud container clusters upgrade <cluster_name> 

Upgrade a specific node pool
****************************
gcloud container clusters upgrade <cluster_name> --node-pool <pool_name>

Deleting a node pool
********************
gcloud container node-pools delete <pool_name> --cluster <cluster_name>


Node auto-provisioning
**********************
gcloud beta container clusters update <cluster_name> --enable-autoprovisioning \
--max-cpu x --max-memory xx

--no-enable-autoprovisioning

Stackdriver logging
*******************
-> When logging is enabled, logs are stored in dedicated persistent datastore
-> Admin activity log, data access log, events log
-> k8s_cluster, gke_cluster, gke_container, gke_nodepool
-> For container and system logs, GKE deploys a per-node logging agent that reads container logs, adds helpful metadata and the stores them
-> Logging agents checks container logs in standard output and standard error logs, kubelet and container runtime logs and logs from system components
-> For events, GKES uses a deployment in kube-system namespace which automatically collects events and sends them to logging

Enable cluster logging
**********************
--enable-cloud-logging

Enable logging for existing cluster
***********************************
gcloud container clusters update <cluster_name> --logging-service logging.googleapis.com


Disable logging for existing cluster
************************************
gcloud container clusters update <cluster_name> --logging-service none


Enable cluster monitoring
*************************
--enable-cloud-monitoring

Enable monitoring for existing cluster
**************************************
gcloud container clusters update <cluster_name> --monitoring-service monitoring.googleapis.com


Maintenance Window
******************
-> A 4 hour timeframe that you choose in which automatic upgrades should occur
->  Creating a maintenance window instructs GKE to automatically trigger automated master upgrades, IP rotations and node pool upgrades during the chosen timeframe 

gcloud container clusters create <cluster_name> --maintenance-window <hh:mm>


Cluster labels (max of 64)
**************************
-> Team or cost center cluster labels
-> Component cluster labels
-> environement or stage cluster labels
-> state cluster labels

