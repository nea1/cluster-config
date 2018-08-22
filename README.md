# cluster-config


kubectl -n kube-system create sa tiller

kubectl create clusterrolebinding tiller-cluster-rule \
    --clusterrole=cluster-admin \
    --serviceaccount=kube-system:tiller
    
helm init --skip-refresh --upgrade --service-account tiller
    

KNOWN_HOSTS=`ssh-keyscan github.com`




helm install --name flux \
--set helmOperator.create=false \
--set git.url=git@github.com:nea1/cluster-config.git \
--set git.branch=main1-backend-prod-secure \
--set git.chartsPath=charts \
--set-string ssh.known_hosts="${KNOWN_HOSTS}" \
--namespace flux \
weaveworks/flux


kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2