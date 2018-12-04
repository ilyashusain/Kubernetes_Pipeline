# Kubernetes_Pipeline

```cd``` into the directory. Run:

```kubectl create -f jenkins```

```kubectl create -f nginx```

This will create the pods and services with the specified configurations. Next, ssh into the nginx pod:

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
