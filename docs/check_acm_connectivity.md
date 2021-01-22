
```
[root@acm-bastion ~]# nslookup bastion.az.asimov.lab
Server:         168.63.129.16
Address:        168.63.129.16#53

Non-authoritative answer:
Name:   bastion.az.asimov.lab
Address: 10.10.99.4
```

```
[root@acm-bastion ~]# nslookup api.ocp4.az.asimov.lab
Server:         168.63.129.16
Address:        168.63.129.16#53

Non-authoritative answer:
Name:   api.ocp4.az.asimov.lab
Address: 10.10.1.5
```

```
# nslookup test.apps.ocp4.az.asimov.lab
Server:         168.63.129.16
Address:        168.63.129.16#53

Non-authoritative answer:
Name:   test.apps.ocp4.az.asimov.lab
Address: 10.10.10.4
```

* The acm bastion have connectivity through the PIP of the bastion and through the proxy

```
[root@acm-bastion ~]# curl www.google.com -I
HTTP/1.1 200 OK
```
