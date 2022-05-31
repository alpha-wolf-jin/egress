# egress

**Git**

```
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/alpha-wolf-jin/egress.git
git config --global credential.helper 'cache --timeout 7200'
git push -u origin main

git add . ; git commit -a -m "update README" ; git push -u origin main
```


```
$ oc get hostsubnet
NAME                                         HOST                                         HOST IP        SUBNET          EGRESS CIDRS   EGRESS IPS
ip-10-0-131-121.us-east-2.compute.internal   ip-10-0-131-121.us-east-2.compute.internal   10.0.131.121   10.129.0.0/23                  
ip-10-0-139-246.us-east-2.compute.internal   ip-10-0-139-246.us-east-2.compute.internal   10.0.139.246   10.128.2.0/23                  
ip-10-0-152-100.us-east-2.compute.internal   ip-10-0-152-100.us-east-2.compute.internal   10.0.152.100   10.128.0.0/23                  
ip-10-0-210-241.us-east-2.compute.internal   ip-10-0-210-241.us-east-2.compute.internal   10.0.210.241   10.130.0.0/23                  
ip-10-0-217-11.us-east-2.compute.internal    ip-10-0-217-11.us-east-2.compute.internal    10.0.217.11    10.131.0.0/23                  
ip-10-0-223-145.us-east-2.compute.internal   ip-10-0-223-145.us-east-2.compute.internal   10.0.223.145   10.129.2.0/23              

$ oc get node | grep worker
ip-10-0-139-246.us-east-2.compute.internal   Ready    worker   42m   v1.23.5+3afdacb
ip-10-0-217-11.us-east-2.compute.internal    Ready    worker   43m   v1.23.5+3afdacb
ip-10-0-223-145.us-east-2.compute.internal   Ready    worker   42m   v1.23.5+3afdacb

$ oc patch hostsubnets ip-10-0-139-246.us-east-2.compute.internal ip-10-0-217-11.us-east-2.compute.internal ip-10-0-223-145.us-east-2.compute.internal --type=merge -p '{"egressCIDRs": ["10.0.224.128/25"]}'

$ oc get hostsubnet
NAME                                         HOST                                         HOST IP        SUBNET          EGRESS CIDRS          EGRESS IPS
ip-10-0-131-121.us-east-2.compute.internal   ip-10-0-131-121.us-east-2.compute.internal   10.0.131.121   10.129.0.0/23                         
ip-10-0-139-246.us-east-2.compute.internal   ip-10-0-139-246.us-east-2.compute.internal   10.0.139.246   10.128.2.0/23   ["10.0.224.128/25"]   
ip-10-0-152-100.us-east-2.compute.internal   ip-10-0-152-100.us-east-2.compute.internal   10.0.152.100   10.128.0.0/23                         
ip-10-0-210-241.us-east-2.compute.internal   ip-10-0-210-241.us-east-2.compute.internal   10.0.210.241   10.130.0.0/23                         
ip-10-0-217-11.us-east-2.compute.internal    ip-10-0-217-11.us-east-2.compute.internal    10.0.217.11    10.131.0.0/23   ["10.0.224.128/25"]   
ip-10-0-223-145.us-east-2.compute.internal   ip-10-0-223-145.us-east-2.compute.internal   10.0.223.145   10.129.2.0/23   ["10.0.224.128/25"]   

$ oc new-project egress

$ oc patch netnamespace egress --type=merge -p '{"egressIPs": ["10.0.224.130"]}'

$ oc get hostsubnet
NAME                                         HOST                                         HOST IP        SUBNET          EGRESS CIDRS          EGRESS IPS
ip-10-0-131-121.us-east-2.compute.internal   ip-10-0-131-121.us-east-2.compute.internal   10.0.131.121   10.129.0.0/23                         
ip-10-0-139-246.us-east-2.compute.internal   ip-10-0-139-246.us-east-2.compute.internal   10.0.139.246   10.128.2.0/23   ["10.0.224.128/25"]   
ip-10-0-152-100.us-east-2.compute.internal   ip-10-0-152-100.us-east-2.compute.internal   10.0.152.100   10.128.0.0/23                         
ip-10-0-210-241.us-east-2.compute.internal   ip-10-0-210-241.us-east-2.compute.internal   10.0.210.241   10.130.0.0/23                         
ip-10-0-217-11.us-east-2.compute.internal    ip-10-0-217-11.us-east-2.compute.internal    10.0.217.11    10.131.0.0/23   ["10.0.224.128/25"]   
ip-10-0-223-145.us-east-2.compute.internal   ip-10-0-223-145.us-east-2.compute.internal   10.0.223.145   10.129.2.0/23   ["10.0.224.128/25"]   ["10.0.224.130"]

# On the node ip-10-0-223-145.us-east-2.compute.internal
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
    link/ether 02:ee:34:1d:ec:e0 brd ff:ff:ff:ff:ff:ff
    inet 10.0.223.145/17 brd 10.0.255.255 scope global dynamic noprefixroute ens5
       valid_lft 1834sec preferred_lft 1834sec
    inet 10.0.224.130/17 brd 10.0.255.255 scope global secondary ens5:eip
       valid_lft forever preferred_lft forever
    inet6 fe80::7a41:7680:d6db:9197/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```


## Setup POD in the egress project

Will use curl command inside pod to access web. Expect web should record egress IP access.

```
$ echo hello-world-01 >index-01.html

$ vim deploy-http.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web
    app.kubernetes.io/component: web
    app.kubernetes.io/instance: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      deployment: web
  template:
    metadata:
      labels:
        deployment: web
    spec:
      containers:
      - image: registry.redhat.io/rhel8/httpd-24:1-161.1638356842
        name: web
        ports:
        - containerPort: 8080
          protocol: TCP
        - containerPort: 8443
          protocol: TCP
        resources: {}
        volumeMounts:
        - name: index-html
          mountPath: /var/www/html/index.html
          readOnly: true
          subPath: index.html
      volumes:
      - configMap:
          defaultMode: 420
          items:
          - key: index.html
            path: index.html
          name: index-html
        name: index-html

$ oc apply -f ./deploy-http.yaml 

$ oc get pod
NAME                   READY   STATUS    RESTARTS   AGE
web-68667fc959-wrhmp   1/1     Running   0          20s

```

# Set up web 

```
$ oc new-project web

$ echo "Egress Test"  >index-02.html

$ oc create configmap index-html --from-file=index.html=./index-02.html

$ oc apply -f ./deploy-http.yaml 

$ oc get pod
NAME                   READY   STATUS    RESTARTS   AGE
web-68667fc959-5rpsr   1/1     Running   0          13s

$ oc get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
web    1/1     1            1           35s

$ oc expose deployment.apps/web

$ oc get svc
NAME   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
web    ClusterIP   172.30.223.217   <none>        8080/TCP,8443/TCP   6s

$ oc expose svc web

$ oc get route
NAME   HOST/PORT                                                 PATH   SERVICES   PORT     TERMINATION   WILDCARD
web    web-web.apps.cluster-87rmw.87rmw.sandbox817.opentlc.com          web        port-1                 None



```

Note:  there is no access log
