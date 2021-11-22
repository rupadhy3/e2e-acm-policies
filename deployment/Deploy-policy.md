Deploy a policy:
To deploy a policy 2 stesp are required to be done on teh HUB cluster:

1. One time step: is to apply the placementrule-<type>.yaml file to teh cluster, which will setup the namespace and PlacementRule for specified type, where type is configuration, access, system ot solo.

2. Next step is to deploy the policy itself, for this choose the policy to apply and apply it from githun file for that policy<policyname>.yaml.