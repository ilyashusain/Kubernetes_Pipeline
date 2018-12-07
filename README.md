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

Now we want to configure the nginx configuration. In linux, our configuration files are usually stored in the ```/etc``` directory. Change your current working directory to ```/etc/nginx``` and run an ```ll```, you will see an ```nginx.conf``` file, this is the configuration file for nginx. Edit the ```nginx.conf``` file by deleting its contents and copying in:

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

Next, reload nginx to apply changes with ```nginx -s reload``` while still in the nginx ssh. We can now access the jenkins ClusterIP service by means of a reverse proxy with the nginx pod, although the jenkins settings are not imported yet (see the following section).

The ```location /``` means that we can just enter ```http://<pod IP>``` into a browser to access it. If instead it were ```location /jenkins``` for example, then we would need to enter ```http://<pod IP>/jenkins``` into the browser to achieve access.

## Configure the jenkins jobs folder

Now we need to configure the permissions on the jenkins settings in the jenkins pod so that jenkins can access it. Access the jenkins pod with:

```kubectl exec -it jenkins-696786d79d-4jgqr bash```

We will be working within the ```/var``` directory, this is where important application settings are usually stored. Change your current working directory to ```/var/jenkins_home``` and run an ```ll```. You'll notice that the ```jobs``` folders ownership is set to ```root:root```, this is why our jenkins will not be functional in the browser at this time (although we can access still access the jenkins pod). Change the ownership on the jobs folder so that jenkins owns it:

```sudo chown jenkins:jenkins jobs```

The ```jobs``` folder contains our ```config.xml``` file, so it is of critical importance.

## Restart the deployment:

```kubectl delete -f jenkins/04_deployment.yml```

```kubectl create -f jenkins/04_deployment.yml```

We can now access the jenkins service by simply copying the nginx service's ip into a browser.
