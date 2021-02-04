## Check connectivity through the proxy

* Check the http_proxy inside of the cluster

```
[root@bastion ~]# oc run debug-pod --image registry.access.redhat.com/rhel7/rhel-tools -i --tty --rm
If you don't see a command prompt, try pressing enter.
[root@debug-pod /]# curl
curl: try 'curl --help' or 'curl --manual' for more information
```

Curl with the -m without the proxy enabled
```
[root@debug-pod /]# curl https://rcarrata.com -I -m 3
curl: (7) Failed connect to rcarrata.com:443; Operation now in progress
```

Export inside the debug-pod the proxy
```
[root@debug-pod /]# export http_proxy=http://bastion.az.asimov.lab:3128
[root@debug-pod /]# export https_proxy=http://bastion.az.asimov.lab:3128
```

Check that the connectivity is running ok
```
[root@debug-pod /]# curl https://rcarrata.com -I -m 3
HTTP/1.1 200 Connection established

HTTP/1.1 200 OK
```

