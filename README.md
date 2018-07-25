# cluster-config

1. If wanting to use helm operator: Add github.com to ssh known_hosts for all flux containers (for some reason the flux operator does this itself but the helm operator doesn't so if you want to use it you need to do this)

	```
	KNOWN_HOSTS=`ssh-keyscan github.com`
	```
	
1. Install flux (configured to use branch `main1-backend-lab-secure` of `cluster-config` repo, with helm charts in `charts` and the `known_hosts` above). Omit the helmOperator and ssh.known_hosts if not using the helm operator.

	```
	helm repo add weaveworks https://weaveworks.github.io/flux
	
	helm install --name flux \
	--set helmOperator.create=true \
	--set git.url=git@github.com:nea1/cluster-config.git \
	--set git.branch=main1-backend-lab-secure \
	--set git.chartsPath=charts \
	--set-string ssh.known_hosts="${KNOWN_HOSTS}" \
	--namespace flux \
	weaveworks/flux
	```	

1. Get flux's public ssh key (is shared by flux and flux-helm-operator):

	```
	kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2
	```

1. Add this as a deploy key to github with read/write permissions

# Testing charts locally

```
helm install charts/beta --name beta
helm upgrade beta charts/beta --set images.0.weight=99 --set images.1.weight=1 --dry-run --debug

```