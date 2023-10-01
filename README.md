## Introduction
These instructions are used to install helm on k8s cluster and setup mysql

# Make sure k8s is setup along with worker nodes
Follow below mentioned commands using :
```
bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
# Installing mysql using helm charts
At first add repo and upgrade repo
```
bash
helm repo add stable https://charts.helm.sh/stable
helm repo update
```
# Now, we can either install directly or pull the chart as well
```
bash
helm pull/install --NAME stable/mysql
```
# replace --name with whatever name we want to give to the pod

Now if we pulled a chart then,
```
bash
tar -xzvf tar -xzvf mysql-1.6.9.tgz
```

Now we will have mysql repo in our local directory, we can now check pods if its working:
```
bash
kubectl get pods
```

========== FOLLOW ONLY IF MYSQL POD IS NOW IN RUNNING STATE ==========

# If mysql pod state is running then eveything is good to go, otherwise follow below:
```
bash
kubectl get pvc
kubectl edit PVCNAME
```

# Editing vpc file before proceeding, below mentioned changes need to be applied.
Extract the pvc file created automatically by helm charts using:
```
bash
kubectl get pvc PVCNAME -o yaml > PVCNAME.yaml
```

Now pvc file will be availabe locally, so delete the pvc running and edit the pvc file.
```
bash
kubectl delete pvc PVCNAME
nano PVCNAME.yaml
```

#Delete the finalizers line from pvc file
```
bash 
 finalizers:
  - kubernetes.io/pvc-protection
```

# Now add pv name, storage class and storage size , make sure it matches with the pv file and storage-class file undee spec section :
```
bash
  volumeName: mysql-pv
  storageClassName: standard
      storage: 10Gi  (keep the size same as pv described)
````

# Now run the SC, PV, PVC and then check if everything is fine
```
bash
kubectl apply -f storage-class.yaml
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-pvc.yaml
```

# Final check
```
bash
kubectl get pods
```
## Now mysql shoul be up and running, if any issue still persist describe the pod and resolve
