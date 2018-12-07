# Kubernetes_Pipeline

## Create the pods and services

```cd``` into the directory. Run:

```kubectl create -f jenkins```

```kubectl create -f nginx```

```kubectl create -f cluster-admin-role-binding.yml```

This will create the pods and services with the specified configurations.

## Configure nginx

Ssh into the nginx pod:

```kubectl exec -it nginx-6688cb9fcb-2bzsn bash```

Update package manager with ```apt update```, then install vim:

```apt install vim```

Now we want to configure the nginx configuration. Change to the ```/etc/nginx``` and edit the ```nginx.conf``` to contain:

```
events {}
http {
        server {
                listen 80;
                location / {
                        proxy_pass http://jenkins:8080/;
                }
        }
}
```

Now reload nginx to apply changes with ```nginx -s reload```. We can now access the jenkins ClusterIP service by means of a reverse proxy with the nginx pod. The ```location /``` means that we can just copy the pod's IP into a browser to access it. If instead it were ```location /jenkins``` then we would need to enter ```<pod IP>/jenkins``` into the browser for access.

## Configure the jenkins jobs folder

Access the jenkins pod:

```kubectl exec -it jenkins-696786d79d-4jgqr bash```

Change to the ```/var/jenkins_home``` directory and run ```ls -al```. You'll notice that the ```jobs``` folders ownership is set to ```root:root```, this is why our jenkins will not work in the browser at this time. Change the ownership on the jobs folder so that jenkins owns it:

```sudo chown jenkins:jenkins jobs```

The ```jobs``` folder contains our ```config.xml``` file, so it is of critical importance.

## Restart the deployment:

```kubectl delete -f jenkins/04_deployment.yml```

```kubectl create -f jenkins/04_deployment.yml```

We can now access the jenkins service by simply copying the nginx service's ip into a browser.
