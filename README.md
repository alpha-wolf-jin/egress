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

```
