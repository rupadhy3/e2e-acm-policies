Ansible Automation Integration
==============================

Ansible automation integrates with RHACM using Ansible Tower or AWX.
Here we will integrate AnsibleJobs with policy violations.

Pre-requisites
===============
1. Ansible Tower (AWX) URL.
2. Ansibel Tower web UI username / password.
3. Ansible Tower (AWX) access token for API requests.
4. Hub cluster must have the Ansibel Automation Platform Resource Operator installed.

Install Ansible Automation Platform Resource Operator on HUB cluster
=====================================================================

 $ oc create namespace ansible-resource-operator
 
 $ cat >> ansible-apr-operator.yaml << EOF
 \-\-\-
 apiVersion: operators.coreos.com/v1alpha1
 kind: Subscription
 metadata:
   name: awx-resource-operator
   namespace: ansible-resource-operator
 spec:
   channel: release-0.1
   installPlanApproval: Automatic
   name: awx-resource-operator
   source: redhat-operators
   sourceNamespace: openshift-marketplace
   startingCSV: awx-resource-operator.v0.1.1
 EOF

 $ oc apply -f ansible-apr-operator.yaml

Ansible Tower (AWX) Application / Cluster Lifecycle Integration
----------------------------------------------------------------
You can configure Ansible Tower (AWX) Jobs to run as your RHACM Cluster / Application deploys. The Ansible job can run as a prehook as well as ansible job can run as a posthook. The prehook runs before the cluster/ application resources start the deployment process while the posthook job runs as soon as the resources are deployed.

Setting up Authentication
=========================
To allow RHACM to access Ansible Tower (AWX) you need to setup a Namespace scoped secret for RHACM to authenticate against Tower (AWX). The secret contains teh Ansible Tower (AWX) URL and Access Token.

 Create the namespace on the HUB cluster for the managed cluster or the appliaction
 $ oc create namespace <xyz>

 To create the secret, navigate to Credentials -> Add credentials -> Red Hat Ansible Automation Platform in the RHACM UI and fill the next fields -

 Credentials name: ansible-tower
 Namespace: mariadb
 and Press Next.

 At the next screen, specify the Ansible Tower host and Ansible Tower token.
 Press Next. Review the information, and press on Add.


Ansible Tower (AWX) Governance Integration
===========================================
Setting up Authentication
--------------------------
Same as above with only difference that here you need to first create the policy namespace like for the configuration policies create the ns-rhacm-managedcluster-config-policies namespace, and create the Ansible Tower (AWX) secret in that namespace, as above.


Policy Automation Example:
==========================

1. Delete Namespace if policy violates
--------------------------------------
In this example we have created a policy that monitors whether a forbidden namespace exists. If the namespace exists a violation will be initiated. Once the violation is initiated an Ansible Job Template will be triggered. The Ansible Job Template will remediate the violation using an Ansible role. A role has already been configured for this scenario at - ansible-playbooks/roles/ocp-namespace.

Configure the Policy
--------------------
Apply the policy to the hub cluster under ns-rhacm-managedcluster-solo-policies namespace

 $ oc project ns-rhacm-managedcluster-solo-policies
 $ oc apply -f <git-url-to-the-example-namespace-violation-policy>

 NOTE: Namespace, placementRule and Ansible Tower (AWX) secret already exists (or is created) in the namespace.

Test the policy
---------------
Create a namespace with the name forbidden-namespace on the managed cluster.

 $ oc create namespace forbidden-namespace

Configure PolicyAutomation
--------------------------
Once policy is in place, craete a policyAutomation object that will initiate an Ansible job that will remediate teh violation. 

 apiVersion: policy.open-cluster-management.io/v1beta1
 kind: PolicyAutomation
 metadata:
   name: namespace-policy-automation
   namespace: rhacm-policies
 spec:
   automationDef:
     extra_vars:
       k8s_api_url: <OCP API server URL>
       k8s_password: <OCP password>
       k8s_username: <OCP username>
     name: K8S-Namespace
     secret: ansible-tower
     type: AnsibleJob
  mode: once
  policyRef: policy-remove-dangerous-namespace


Modify the parametes relevant to your cluster and create the PolicyAutomation object on the hub cluster in the ns-rhacm-managedcluster-solo-policies namespace. As soon as you create the PolicyAutomatiob object, an AnsibleJob object is created in the ns-rhacm-managedcluster-solo-policies namespace. the AnsibleJob marks that the Ansible job template on teh AnsibleTower (AWX) has been initiated.

 $ oc get ansiblejob -n rhacm-policies


If you log into the Ansible Tower web interface, you'll notice that the K8S-Namespace Job Template has been initiated. The Job indicates that the forbidden namespace has been removed.

Now, take a look at the Governance dashboard in RHACM. Note that the violation is no longer present in the policy you have created. The forbidden namespace is no longer present.


