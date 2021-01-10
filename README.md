[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/569/badge)](https://bestpractices.coreinfrastructure.org/projects/569)

##  Deploy & Configure Kasten K10 by Veeam and a Quick Backup & Restore Demo on ⎈ Kubernetes / ⎈ Tanzu or ⎈ Openshift

*Written by Kerem ÇELİKER*
- Linkedin: **`linkedin.com/in/keremceliker`**
- Twitter: **`@CloudRss`**
- Blog: **`www.keremceliker.com`**

# 🧭 INTRODUCTION 

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/dashboard.png"> 


Veeam has announced acquisition of Kasten in 5 October 2020, last year, Kasten the leader in Data Backup, Disaster Recovery and Mobility for Kubernetes and Container Area.

Kastan K10 Software designed to run and get backup natively on Kubernetes, Openshift or OpenStack to protect applications and namespaces. K10 integrates seamlessly with Any Supported Vendor Product's CSI Driver for Kubernetes for native-support.

# 📜 What About Licenses Types and Price for Kasten K10: (via Official Source)

**There are 2 type Licences:**

- Starter License 

- Enterprise License

**Starter License:**

K10 comes with an embedded Starter Edition license by default. The starter edition license allows you to use the software on a cluster with at most 50 worker nodes for one month and then 10 nodes after the one month period

**Enterprise License:**

Enterprise Edition obtaining All Unlimited Cool Features for protecting your Enterprise Grade Applications and Valuable Products.

# 💬❓ Frequent License Question About Kasten K10:

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/faq.png"> 


- **(Q-1) If Kasten K10 license expired because of grace-period, What does happens to my previously all configured policies ?**

Nothing's Happens :) Kasten K10 will definitely continue to run getting previous backup for next 50 days for customer satisfaction and importance to your applications. Only you can't just define new backups as you know and it's so normal and understandable anyway for sure :)

- **(Q-2) Do you mind if We try K10 in our Kubernetes,Tanzu or Openshift Environment ?**

No, You can try for FREE no sign-up needed :)

**Link:** https://www.kasten.io/try-kasten-k10


==============================================================================

## 📚 Let's go Install Kasten K10 now, take a break from ads, right? 

**Because, I can hear you saying enough advertising KEREM !😁**


**📍STEP 1**

Install HELM 3 or HELM 2 named "Bundle Package Orchestrator Manager" if you don't have it on your Core-Kubernetes Stack, you must download and install from here: https://github.com/helm/helm.

The most common problem during helm use is the taking of Tiller package errors. This problem is generally simple to resolve. 

First of all, you must bring your Helm & Tiller Pack to the latest version and give Tiller authorized permission in RBAC on your Helm Repo packages. That's how these mistakes are mostly handled.. :)

`sudo snap install helm --classic` (*works only Ubuntu,I guess..)`

**📍STEP 2**

The CSI external-snapshotter sidecar only watches for VolumeSnapshotContent create/update/delete events. Also Kubernetes external snapshotter “watches Kubernetes Snapshot CRD objects and triggers CreateSnapshot/DeleteSnapshot against a CSI endpoint”

`$ git clone https://github.com/kubernetes-csi/external-snapshotter`

**📍STEP 3**

`
$ cd external-snapshotter
$ git checkout release-2.0
$ kubectl apply -f config/crd -f deploy/kubernetes/snapshot-controller
`

**📍STEP 4**


```yaml

$ kubectl create -f-

apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshotClass
metadata:
  name: vmwarecsi-snapshot
  annotations:
    snapshot.storage.kubernetes.io/is-default-class: "true"
driver: csi.k8s.com
deletionPolicy: Delete
parameters:
  description: "Snapshot created by the VMware/RedHat or Any Supported Vendor CSI Driver"
  csi.storage.k8s.io/snapshotter-secret-name: vmware-backend
  csi.storage.k8s.io/snapshotter-secret-namespace: kube-system
```  
`Note:` **Please refer to this site for CSI Yaml Config. Because mine's just an example,no guaranteed of your side as you know.**

For VMware:
https://vsphere-csi-driver.sigs.k8s.io/

**There are so many vendor has CSI Driver for Kubernetes Stack, Please check the Vendors Official Support Site.**


<img src="https://blog.kasten.io/hs-fs/hubfs/Blog%20Images/VMware-Kasten%20Data%20Management%20for%20Kubernetes%20and%20Modern%20Applications/vSphere%20with%20Native%20Kubernetes.png?width=899&name=vSphere%20with%20Native%20Kubernetes.png"> 



**📍STEP 5**

https://docs.kasten.io/latest/install/storage.html#csi-snapshot-configuration

`$ kubectl annotate volumesnapshotclass hpe-snapshot k10.kasten.io/is-snapshot-class=true`

===========

- You need to check and configure before you install Kasten's K10 platform on Kubernetes !

`$ curl https://docs.kasten.io/tools/k10_primer.sh | bash`

```yaml
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0Namespace option not provided, using default namespace
100  6226  100  6226    0     0  12502      0 --:--:-- --:--:-- --:--:-- 12502
Checking for tools
 --> Found kubectl
 --> Found helm
Checking if the Kasten Helm repo is present
Error: Couldn't load repositories file (/root/.helm/repository/repositories.yaml).
 --> The Kasten Helm repo was not found
Checking for required Helm Tiller version (>= v2.16.0)
Error: could not find tiller
 --> Error when running 'helm version'
Preflight checks failed
```

======================================================================================

**📍STEP 6**

`$helm init`

- Note: If tiller is already installed please use helm init --client-only or helm init --client-only --skip-refresh

- If you have "Service Account tiller not found" error in ReplicateSet, you can use this RBAC.yaml for quick-fix ;)

```yaml
$ cat tiller-rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
 name: tiller
 namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
 name: tiller
roleRef:
  apiGroup: rbac.authotization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```
    
**OR**

You can use it by cli as the below:

```
kubectl --namespace kube-system create sa tiller
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
```
**📍STEP 7**

- Create kasten-io namespace if have not done it yet (Optional-It's not necessary)

`kubectl create namespace kasten-io`

**📍STEP 8**

- Install add Kasten K10 Helm Repo on Kubernetes or Tanzu, Openshift related Distribution;

`helm repo add kasten https://charts.kasten.io/`

`helm repo update` => It's Important !!

**📍STEP 9**

- Let's Start to Install Kasten K10 by Veaam 

`helm install kasten/k10 --name=k10 --namespace=kasten-io --set persistence.storageClass=hostpath`


**📍STEP 10**

- Check the K10 Pods until pods appears running-state

watch -n5 kubectl get pods --namespace kasten-io

```yaml
kubectl get pods --namespace kasten-io
NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kasten-io     aggregatedapis-svc-f72d26hn8-8zsr0      1/1     Running   0          4m12s
kasten-io     auth-svc-8d26hc6461-9c9fb               1/1     Running   0          4m12s
kasten-io     catalog-svc-c71103fkp-62tyl             2/2     Running   0          4m12s
etc...
```
You should see apx. that 20 Pods working running state as the above !

**📍STEP 11**

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/2.png"> 

- Configure port forwarding for the gateway service to allow access for Kasten Web Browser Interface.

`kubectl --namespace kasten-io port-forward service/gateway 8080:8000`

- The K10 dashboard will be available at `http://127.0.0.1:8080/k10/#/`

`kubectl --namespace kasten-io port-forward service/gateway 8080:8000`

========================================================================================

## Now it's time to Install a Sample Database App and Delete Table and The Most Delicious Final Part Backup & Restore A Table from MARIADB 😉

**📍STEP 1**

```yaml
kubectl create ns mariadb 
```

You can take also backup with MySQL as the follows below link:

**Link:** https://blog.kasten.io/kubernetes-backup-and-restore-for-mysql


**📍STEP 2**

```yaml
helm install -f values.yaml -n mariadb keremceliker-db bitnami/mariadb
```


**📍STEP 3**

You can learn Maria Database Root => Password as the follows below:

```yaml
kubectl get secret --namespace mariadb keremceliker-db -o jsonpath="{.data.mariadb-root-password}" | base64 --decode
```

**📍STEP 4**

Run a Pod that you can use as a client

```yaml
kubectl run keremcelker-db-client --rm --tty -i --restart='Never' --image docker.io/bitnami/mariadb:10.5.6-debian-10-r0 --namespace mariadb --command -- bash

mysql -h keremceliker-db.mariadb.svc.cluster.local -u root -p K@$t€n2021!
```

**📍STEP 5**

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/kasten-enterprise-dashboard.png"> 


**📍STEP 6**

Click to details tab for View application details and related resources.

**📍STEP 7**

```yaml
git clone https://github.com/datacharmer/test_db
cd test_db

Login to MySQL Server:

mysql -h 127.0.0.1 -u root -p admin -P 3306
```

**📍STEP 8**

```yaml
mysql> show databases

add database chart picture

exit

mysql -h 127.0.0.1 -u root -p admin -P 3306 < employees.sql

mysql -h 127.0.0.1 -u root -p admin -P 3306 -t < test_employees_sha.sql
```

Please Check the MariaDB Chart Picture !

**📍STEP 9**

Click a Snapshot as Get Backup for Manually create a restore point for this application

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/3.png"> 
<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/4.png"> 


**Important:** Kasten can also take snapshots of applications and their settings.


**📍STEP 10**

```yaml
kubectl get -n mariadb volumesnapshots

mysql -h 127.0.0.1 -u root -p admin -P 3306

mysql> show databases
```

Please Check the MariaDB Tables Chart !

```yaml
mysql>  use employees;
mysql> delete from departments;
```

**📍STEP 11**

Please Check the Deleted MariaDB Database in CHART !

```yaml
mysql -h 127.0.0.1 -u root -p admin -P 3306 -t < test_employees_sha.sql
```

**📍STEP 12**

Finally, we must view restore points for mariadb and Restore to the same or different namespace...

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/8-1.png"> 



```yaml
kubectl get --raw /apis/apps.kio.kasten.io/v1alpha1/namespaces/mariadb/restorepointcontents/keremceliker-mariadb-sib539/details
```

<img src="https://github.com/keremceliker/Deployment-Kasten-K10-and-a-Sample-Demo-on-Kubernetes/blob/main/7.png"> 


List restore point in namespace 'MariaDB-App'


```yaml
kubectl get restorepoints.apps.kio.kasten.io --namespace mariadb
```

**📍STEP 13**


Please Check the MariaDB Datbases Databases Chart After Restore !


```yaml
mysql -h 127.0.0.1 -u root -p admin -P 3306 -t < test_employees_sha.sql
```

## 📚 REFERENCES:

- https://docs.kasten.io/latest/install/advanced.html

- https://docs.kasten.io/latest/install/advanced.html#using-red-hat-certified-upstream-container-images

- https://docs.kasten.io/latest/kanister/mysql/install.html

- https://github.com/kubernetes-csi/external-snapshotter

