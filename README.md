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

Now reload nginx to apply changes with ```nginx -s reload```.

## Configure the jenkins jobs folder

Access the jenkins pod:

```kubectl exec -it jenkins-696786d79d-4jgqr bash```

Change to the ```/var/jenkins_home``` directory and change the ownership on the jobs folder as such:

```sudo chown jenkins:jenkins jobs```

## Restart the deployment:

```kubectl delete -f jenkins/04_deployment.yml```

```kubectl create -f jenkins/04_deployment.yml```

We can now access the jenkins service by simply copying the nginx pod's ip into a browser.
