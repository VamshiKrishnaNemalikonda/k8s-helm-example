Installing helm - install helm where kubectl CLI works(k8s cluster master node ?)
---------------------------------------------------------------------------------
wget https://get.helm.sh/helm-v2.16.5-linux-amd64.tar.gz <br/>
tar -xvzf helm-v2.16.5-linux-amd64.tar.gz <br/>
helm init --upgrade <br/>

Install grafana using helms from stable repo
--------------------------------------------
helm search grafana <br/>
helm search <br/>
helm inspect stable/grafana <br/>
helm install stable/grafana --name mygrafana --set service.type=NodePort <br/>
kubectl get pods <br/>
kubectl get services <br/>
kubectl describe svc mygrafana <br/>
export NODE_PORT=$(sudo kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services mygrafana) <br/>
echo $NODE_PORT <br/>
export NODE_IP=$(sudo kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}") <br/>
echo $NODE_IP - 10.0.0.52 (this is not an extenal IP address, since this VM is instantiated from Openstack I've used corresponding floating IP addr-10.211.1.130) <br/>
echo http://$NODE_IP:$NODE_PORT - Check the grafana is up and running <br/>

login to grafana using defalt credentials  <br/>
username : admin <br/>
password : <kubectl get secret --namespace default mygrafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo> <br/>

Upgade grafana helm pacakge
---------------------------
helm list <br/>
helm upgrade mygrafana stable/grafana --reuse-values --set replicas=2 <br/>
kubectl get pods <br/>
helm list <br/>
NAME            REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE <br/>
mygrafana       2               Tue Apr 14 09:26:25 2020        DEPLOYED        grafana-4.2.2   6.5.2           default <br/>

Delete grafana helm pacakge
---------------------------
helm delete mygrafana <br/>
helm list -all <br/>
helm delete --purge mygrafana <br/>
helm list --all <br/>

Create custom helm package
--------------------------
helm create myhelmchartplanet <br/>
cd myhelmchartplanet <br/>
-rw-r--r-- 1 ubuntu ubuntu 1088 Apr 14 09:43 values.yaml <br/>
drwxr-xr-x 3 ubuntu ubuntu 4096 Apr 14 09:43 templates <br/>
-rw-r--r-- 1 ubuntu ubuntu  113 Apr 14 09:43 Chart.yaml <br/>
drwxr-xr-x 2 ubuntu ubuntu 4096 Apr 14 09:43 charts <br/>

helm lint myhelmchartplanet - validates helm pacakge syntax ? <br/>

Package and install custom helm charts 
--------------------------------------
helm package myhelmchartplanet/ <br />
Successfully packaged chart and saved it to: /home/ubuntu/vamshi/helm_charts/myhelmchartplanet-0.1.0.tgz <br />

helm search myhelmchartplanet <br />
NAME                    CHART VERSION   APP VERSION     DESCRIPTION <br />
local/myhelmchartplanet 0.1.0           1.0             A Helm chart for Kubernetes <br />

helm inspect myhelmchartplanet <br />
helm install myhelmchartplanet <br />
kubectl get pods <br /> 
kubectl describe svc oily-vulture-myhelmchartplanet <br />
helm list <br />
NAME            REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE <br />
oily-vulture    1               Tue Apr 14 11:44:15 2020        DEPLOYED        myhelmchartplanet-0.1.0 1.0             default <br />

Upgrade custom helm pacakge
---------------------------
Make changes to values.yaml to increase the replica count <br/>
Make changes to Chart.yaml to change the package version <br/>

helm package myhelmchartplanet <br/>
Successfully packaged chart and saved it to: /home/ubuntu/vamshi/helm_charts/myhelmchartplanet-0.2.0.tgz <br/>
helm upgrade oily-vulture myhelmchartplanet <br/>
kubectl get pods <br/>
kubectl get svc <br/>

Rollback helm pacakge 
---------------------
helm rollback oily-vulture 1 <br/>
kubectl get pods <br/>
kubectl get svc <br/>

Check the history operations on helm package
--------------------------------------------
helm history oily-vulture <br/>
