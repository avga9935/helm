## Introduction
We are going to setup and deploy Django app using PostreSQL and Django, after creating a Django image using Dockerfile.

# NOTE: DO NOT PROCEED UNTIL DOCKERFILE IS CREATED AND PUSHED INTO THE DOCKERHUB (while running the dockerfile using docker run command container may not start check the logs if logs have issues with the SECRET_KEY then its okay)

# STEP 1 : Creating Dockerfile for django and pushing it ro dockerhub repo. (follow readme under avga9935/helm/django/django_dockerfile)
# Step 2 : Setup and install PostgreSQL
```
kubectl create namespace test
helm install postgresql postgresql -n test
```
# Step 3 : Setup and install django
```
helm install django django -n test
kubectl get sc,pv,pvc,svc,pod,deploy,statefulset -n test
```
## Once everything is done now follow below mentioned commands to proceed
```
bash
kubectl exec -it -n test PODNAME --/bin/sh
# after taking shell
python manage.py makemigrations && python manage.py migrate
python manage.py ANYUSERNAME
# Enter a username, email address, and password for your user
exit
kubectl port-forward svc/django -n test 8000:8000
```
Now access the browser and search for http://127.0.0.1:8000/admin
# USE username and password we created at the time of taking shell
