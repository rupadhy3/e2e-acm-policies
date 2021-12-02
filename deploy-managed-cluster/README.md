RESOURCES FOR DEPLOYING CLUSTER			
API	           			KIND	                       NAMESPACE	        CONTAINS
v1	           			Namespace	               	same as Cluster-name	namespace definition
v1	           			Secret (cloud-credential)      	same as Cluster-name	cloud credentails (like access key and access secret key)
v1	           			Secert (ssh-key)	       	same as Cluster-name	ssh-private key
v1	           			Secret (pull-secret)	       	same as Cluster-name	pull secret
v1	           			Secret (install_config.yaml)   	same as Cluster-name	install_config.yaml file
hive.openshift.io/v1			ClusterImageset (CR)		same as Cluster-name	location for openshift version image
hive.openshift.io/v1			ClusterDeployment (CR)		same as Cluster-name	core CR for deploying cluster
cluster.open-cluster-management.io/v1	ManagedCluster	NA		RHACM resource		Global RHACM resource to specify teh new managed cluster
agent.open-cluster-management.io/v1	KlusterletAddonConfig		same as Cluster-name	RHACM resource for joining managed cluster
hive.openshift.io/v1			MachinePool			same as Cluster-name	Cluster worker pool details 
v1					Secret (cloud-cred-detailed)				Detailed secret (with cloud creds, ssh-keys, pull secret, domain) for RHACM credential resource

