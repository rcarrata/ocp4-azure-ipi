## Check Firewall approach

```
[root@bastion ocp4]# oc get pod -n openshift-authentication oauth-openshift-55b7b69d94-lnhp9
NAME                               READY   STATUS    RESTARTS   AGE
oauth-openshift-55b7b69d94-lnhp9   1/1     Running   0          3h29m
```

```
[root@bastion ocp4]# oc exec -ti oauth-openshift-55b7b69d94-lnhp9  -n openshift-authentication -- bash
[root@oauth-openshift-55b7b69d94-lnhp9 /]#
```

```
[root@oauth-openshift-55b7b69d94-lnhp9 /]# curl https://docs.openshift.com -I
HTTP/1.1 200 Connection established
HTTP/2 200
```

```
[root@oauth-openshift-55b7b69d94-lnhp9 /]# curl https://rcarrata.com
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to rcarrata.com:443
```

```
[root@oauth-openshift-55b7b69d94-lnhp9 /]# curl https://www.bbc.co.uk
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to www.bbc.co.uk:443
```

```
[root@oauth-openshift-55b7b69d94-lnhp9 /]# curl www.google.es
HTTP  request from 10.10.1.4:54502 to www.google.es:80. Action: Deny. No rule matched. Proceeding with default action
```

```
[root@oauth-openshift-55b7b69d94-lnhp9 /]# curl https://www.google.es
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to www.google.es:443
```

```
[root@oauth-openshift-55b7b69d94-lnhp9 /]# curl https://www.google.com -I
HTTP/1.1 200 Connection established
HTTP/2 200
```
