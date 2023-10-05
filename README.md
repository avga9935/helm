## Introduction
These instructions are used to install helm on k8s cluster and setup mysql with wordpress

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
As we already have our own Mysql and wordpress charts so we are going to use those
every zip file is ready to go just unzip # wordpress-0.1.0.tgz and you will get wordpress setup completely just need to use port-forwading command to access it using localhost and create database/user/pass using exec command on mysql pod.
```
bash
tar -xzvf wordpress-0.1.0.tgz
helm install ANYNAME wordpress -n NAMESPACE
