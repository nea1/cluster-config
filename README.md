# cluster-config

1. If wanting to use helm operator: Add github.com to ssh known_hosts for all flux containers (for some reason the flux operator does this itself but the helm operator doesn't so if you want to use it you need to do this)

	```
	ssh-keyscan github.com >> known_hosts
	cat known_hosts
	
	KNOWN_HOSTS='github.com ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAq2A7hRGmdnm9tUDbO9IDSwBK6TbQa+PXYPCPy6rbTrTtw7PHkccKrpp0yVhp5HdEIcKr6pLlVDBfOLX9QUsyCOV0wzfjIJNlGEYsdlLJizHhbn2mUjvSAHQqZETYP81eFzLQNnPHt4EVVUh7VfDESU84KezmD5QlWpXLmvU31/yMf+Se8xhHTvKSCZIFImWwoG6mbUoWf9nzpIoaSjB+weqqUUmpaaasXVal72J+UX2B+2RPW3RcT0eOzQgqlJL3RKrTJvdsjE3JEAvGq3lGHSZXy28G3skua2SmVi/w4yCE6gbODqnTWlg7+wC604ydGXA8VJiS5ap43JXiUFFAaQ=='
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
