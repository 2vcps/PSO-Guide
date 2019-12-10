## Testing PSO with Go Web App and MySQL with PVC

```
kubectl apply -f 1-mysql-deployment-pvc.yaml
# wait for it to initialize the DB, if you are impatient it is ok the web app might fail and restart a few times until the DB responds.
kubectl apply -f 2-gowebapp-depployment-pvc.yaml
```

You can create your own Docker images using the Docker files found in ./gowebapp and ./gowebapp-mysql by default it pulls from a public image.

**Special thanks to the [VMware/Heptio](https://s3.eu-central-1.amazonaws.com/heptio-edu-static/how/gowebapp.tar.gz) team. This is their demo "forked" and modified to use a PVC for the MySQL data**