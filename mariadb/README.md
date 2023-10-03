## Introduction
These instructions are used to install mysql using helm on k8s cluster as a ready to go chart (package) so we don't need to make any changes later on.

# Download mysql repo
```
bash
helm repo add mysql-operator https://mysql.github.io/mysql-operator/
helm repo update
```

# Now unzip the repo downloaded and move in the mysql directory
```
bash
tar -xzvf mysql.tar.gz
cd mysql
```

## Now create pv, pvc and storageclass files so the pvc can be connected,
# NOTE : THESE FILES ARE UNDER TEMPLATE SECTION

# Now macke changes to the values.yaml file so it can read from pv,pvc and sc so storage can be bound and mysql pod will start running instantly
```
persistence:
  enabled: true
  ## database data Persistent Volume Storage Class
  ## If defined, storageClassName: <storageClass>
  ## If set to "-", storageClassName: "", which disables dynamic provisioning
  ## If undefined (the default) or set to null, no storageClassName spec is
  ##   set, choosing the default provisioner.  (gp2 on AWS, standard on
  ##   GKE, AWS & OpenStack)
  ##
  # pvName: "mysql-pv" (# if we know the name of pv then we can use it here but we need to change name under pv file too) (#recommended is to avoid putting a name rather then put a variable so it can pick pv file with any random name we choose)
  storageClass: "-" (# uses conditions which are set under pv,pvc,and sc to set name automatically or choose name if defined already under these files)
  accessMode: ReadWriteOnce
  size: 10Gi (# we can use a number set under pv, pvc no need to provide any arguments)
  annotations: {}
```

# Example :
```
  name: {{ template "mysql.fullname" . }} (# template is the directory under which mysql name will be wither choosen if decided or will provide default name)
  namespace: {{ .Release.Namespace }} (# .Release will provide the name at time time of installation autmatically based upon file)
spec:
  capacity:
    storage: {{ .Values.persistence.size }} (# .values if the file in which under persistence heading go to size and set variables to it can get the storaage size automatically)
```

# Now try to install mysql using helm
```
bash
helm install mysql ../mysql
kubectl get pv,pvc,sc
kubectl get pods
```

## Check if mysql pod is in running state now, if not use describe and troubleshoot accordingly

# Now if we want to create namespace along with installation so we can install the pod under new namespaces then use :
```
bash
helm install mysql ../mysql --create-namespace -namespace NEWNAME
```

