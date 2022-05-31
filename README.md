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
