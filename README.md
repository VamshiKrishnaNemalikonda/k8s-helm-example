Installing helm - install helm where kubectl CLI works(k8s cluster master node ?)
---------------------------------------------------------------------------------
wget https://get.helm.sh/helm-v2.16.5-linux-amd64.tar.gz
tar -xvzf helm-v2.16.5-linux-amd64.tar.gz
helm init --upgrade

Install grafana using helms from stable repo
--------------------------------------------
helm search grafana
helm search
helm inspect stable/grafana
helm install stable/grafana --name mygrafana --set service.type=NodePort
kubectl get pods
kubectl get services
kubectl describe svc mygrafana
export NODE_PORT=$(sudo kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services mygrafana)
echo $NODE_PORT
export NODE_IP=$(sudo kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
echo $NODE_IP - 10.0.0.52 (this is not an extenal IP address, since this VM is instantiated from Openstack I've used corresponding floating IP addr-10.211.1.130)
echo http://$NODE_IP:$NODE_PORT -Check the grafana is up and running

Upgade grafana helm pacakge
---------------------------
helm list
helm upgrade mygrafana stable/grafana --reuse-values --set replicas=2
kubectl get pods
helm list
NAME            REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE
mygrafana       2               Tue Apr 14 09:26:25 2020        DEPLOYED        grafana-4.2.2   6.5.2           default

Delete grafana helm pacakge
---------------------------
helm delete mygrafana
helm list -all
helm delete --purge mygrafana
helm list --all

Create custom helm package
--------------------------
helm create myhelmchartplanet
cd myhelmchartplanet
-rw-r--r-- 1 ubuntu ubuntu 1088 Apr 14 09:43 values.yaml
drwxr-xr-x 3 ubuntu ubuntu 4096 Apr 14 09:43 templates
-rw-r--r-- 1 ubuntu ubuntu  113 Apr 14 09:43 Chart.yaml
drwxr-xr-x 2 ubuntu ubuntu 4096 Apr 14 09:43 charts

helm lint myhelmchartplanet - validates helm pacakge syntax ?

Package and install custom helm charts
--------------------------------------
helm package myhelmchartplanet/
Successfully packaged chart and saved it to: /home/ubuntu/vamshi/helm_charts/myhelmchartplanet-0.1.0.tgz

helm search myhelmchartplanet
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
local/myhelmchartplanet 0.1.0           1.0             A Helm chart for Kubernetes

helm inspect myhelmchartplanet
helm install myhelmchartplanet
kubectl get pods
kubectl describe svc oily-vulture-myhelmchartplanet
helm list
NAME            REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE
oily-vulture    1               Tue Apr 14 11:44:15 2020        DEPLOYED        myhelmchartplanet-0.1.0 1.0             default

Upgrade custom helm pacakge
---------------------------
Make changes to values.yaml to increase the replica count
Make changes to Chart.yaml to change the package version

helm package myhelmchartplanet
Successfully packaged chart and saved it to: /home/ubuntu/vamshi/helm_charts/myhelmchartplanet-0.2.0.tgz
helm upgrade oily-vulture myhelmchartplanet
kubectl get pods
kubectl get svc

Rollback helm pacakge
---------------------
helm rollback oily-vulture 1
kubectl get pods
kubectl get svc

Check the history operations on helm package
--------------------------------------------
helm history oily-vulture
