# e2e-acm-policies
RHACM policies for end-2-end testing of cluster lifecycle
==========================================================

RHACM Resources and how they are organized:

Namespace:
==========
RHACM policies are categorized as Configuration , Access, Security and others, and each have a separate namespace as:
As For configuration: ns-rhacm-managedcluster-config-policies
As For access: ns-rhacm-managedcluster-access-policies
As For security: ns-rhacm-managedcluster-security-policies
As For others(solo): ns-rhacm-managedcluster-solo-policies

NOTE: Namespace and PlacementRule object creationis onetime activity and will be taken care of initially.
      Ploci and Placementbinding will be implemented as GitOps resources.

PlacementRule:
==============
kind: PlacementRule is part of apiVersion: apps.open-cluster-management.io/v1, and defines a name for the said placementRule and on which namespace it will be deployed.

In its spec section it has definitions for clusterConditions, clusterReplicas and clusterSelector. Using these definitions it sets which clusters will be selcted as a rule for placement.

The clustsreConditions section tells which type of managed clusters can be accepted, such as type: ManagedClusterConditionAvailable with status: "True".

The clusterReplicas section defines how many min clusters are bound in the binding like 1 or 2 or 3 etc. (optional)

The clusterSelector section defines matchExpressions as below with key and value, which must resemble with the label of the cluster to select that cluster.
    - key: usage
      operator: In
      values:
      - configrequire

PlacementBinding:
==================
kind: PlacementBinding is part of apiVersion: policy.open-cluster-management.io/v1, and in its metadata section defines a name for the said placementBinding and on which namespace it will be deployed.

In also has a Placementref and Subjects sections. Using these sections Placementref for defining the PlacementRule to get the selected cluster and Subjects to define which policies to bind with the PlacementRule from the PlacementRef section.

In its Placementref section it defines name of the PlacementRule, with kind: PlacementRule and apiGroup of the PlacementRule object.

In its Subjects section it defines name of the policy, with kind: Policy and apiGroup of the Policy object.

Policy:
=======


Here:
=====
1. Configuration Policies:
   a. Configuration related PlacementRule, PlacementBinding and Policy all will go inside the namespace: ns-rhacm-managedcluster-config-policies.

   b. Need to set the correct label as, usage=configrequired on the cluster to get that cluster selected for applyinh configuration policies.

2. Access Policies:
   a. Access related PlacementRule, PlacementBinding and Policy all will go inside the namespace: ns-rhacm-managedcluster-access-policies.

   b. Need to set the correct label as, usage=configrequired on the cluster to get that cluster selected for applyinh configuration policies.

3. System wide policies (like certificate):
   a. System related PlacementRule, PlacementBinding and Policy all will go inside the namespace: ns-rhacm-managedcluster-system-policies.

   b. Need to set the correct label as, usage=configrequired on the cluster to get that cluster selected for applyinh configuration policies.

4. Other Policies (which need to be applied individually for some reason):
   a. Others related PlacementRule, PlacementBinding and Policy all will go inside the namespace: ns-rhacm-managedcluster-solo-policies.
   
   b. Need to set the correct label as, usage=configrequired on the cluster to get that cluster selected for applyinh configuration policies.
