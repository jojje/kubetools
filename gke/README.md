# replace-gke-nodes
Google container engine ("GKE") is currently lacking a feature for removing nodes from a node-pool (node group in kube parlance).

This script allows replacement of one or more (faulty?) nodes from the _same_
node-pool. It also supports a simulation mode, so that it can be used to
produce the commands which you can run yourself, in case you don't trust _some
random_ github user's code to effect changes to your cluster.

*Note*: all listed nodes are assumed to belong to the same nodepool, and the
pool is discovered by querying the first node specified. To replace nodes in
different pools, run the command once for each pool. This behavior is
intentional so that people don't remove nodes in other pools by mistake, since
one typically works on concerns delineated to members of a pool at a time.

## Usage

    $ replace-gke-nodes
    Usage: replace-gke-nodes [-s] NODE_NAME [NODE_NAME...]
    Replaces one or more nodes in a GKE nodepool

    Options:
      -s  Dry-run. Show mutation operations instead of executing them. For debugging.

### Example

    $ replace-gke-nodes -s gke-mycluster-default-pool-f01af341-xcvl
    [*] Dry-run (simulation) mode
    [*] gcloud -q container clusters resize mycluster --node-pool=default-pool --size=3
    [*] Waiting for new nodes to become ready
    [*] kubectl drain gke-mycluster-default-pool-f01af341-xcvl --delete-local-data --force --ignore-daemonsets --grace-period=10
    [*] gcloud -q compute instance-groups managed delete-instances gke-mycluster-default-pool-f01af341-grp --instances=gke-mycluster-default-pool-f01af341-xcvl
    [*] Success!

