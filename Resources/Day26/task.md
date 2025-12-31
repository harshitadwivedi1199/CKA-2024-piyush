## Task 26/40

In this exercise, you will learn Authentication and Authorization in Kubernetes

### Task details
1. Create a new kind cluster by disabling the default CNI
2. Install `Calico` Network Add-on to your Kind cluster
3. Create 3 deployments with as below:
    name: frontend , image-name: nginx , replicas=1 , containerPort
    name: backend , image-name: nginx , replicas=1 , containerPort
    name: db , image-name: mysql , replicas=1 , containerPort: 3306
4. Expose all of these applications on a nodePort service with the same name as the deployment name
5. Do the connectivity test that all of your pods are able to interact with each other.
6. Create a network policy and restrict the access so that only backend pod should be able to access the db service on port 3306.
7. Attach this network policy to the backend deployment

```
controlplane:~$ kubectl get deploy 
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
backend    1/1     1            1           20m
frontend   1/1     1            1           26m
mysql      1/1     1            1           16m

controlplane:~$ cat nginx-frontend.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    role: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      role: frontend
  template:
    metadata:
      labels:
        role: frontend
    spec:
      containers:
      - name: frontend
        image: nginx:latest
        ports:
        - containerPort: 80

controlplane:~$ cat nginx-backend.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  labels:
    role: backend
spec:
  replicas: 1
  selector:
    matchLabels:
      role: backend
  template:
    metadata:
      labels:
        role: backend
    spec:
      containers:
      - name: backend
        image: nginx:latest
        ports:
        - containerPort: 80

controlplane:~$ cat db.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  labels:
    name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      name: mysql
  template:
    metadata:
      labels:
        name: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        env:
        - name: "MYSQL_USER"
          value: "mysql"
        - name: "MYSQL_PASSWORD"
          value: "mysql"
        - name: "MYSQL_DATABASE"
          value: "testdb"
        - name: "MYSQL_ROOT_PASSWORD"
          value: "verysecure"
        ports:
        - name: http
          containerPort: 3306
          protocol: TCP

## frontend

controlplane:~$ kubectl exec -it frontend-797f589899-qpsg8 -- bash 
root@frontend-797f589899-qpsg8:/# apt update
Get:1 http://deb.debian.org/debian trixie InRelease [140 kB]
Get:2 http://deb.debian.org/debian trixie-updates InRelease [47.3 kB]
Get:3 http://deb.debian.org/debian-security trixie-security InRelease [43.4 kB]
Get:4 http://deb.debian.org/debian trixie/main amd64 Packages [9670 kB]
Get:5 http://deb.debian.org/debian trixie-updates/main amd64 Packages [5412 B]
Get:6 http://deb.debian.org/debian-security trixie-security/main amd64 Packages [93.7 kB]
Fetched 10.0 MB in 2s (6657 kB/s)                          
All packages are up to date.    
root@frontend-797f589899-qpsg8:/# apt install telnet 
Installing:                     
  telnet

Installing dependencies:
  inetutils-telnet  netbase

Summary:
  Upgrading: 0, Installing: 3, Removing: 0, Not Upgrading: 0
  Download size: 186 kB
  Space needed: 377 kB / 8840 MB available

Continue? [Y/n] y
Get:1 http://deb.debian.org/debian trixie/main amd64 netbase all 6.5 [12.4 kB]
Get:2 http://deb.debian.org/debian trixie/main amd64 inetutils-telnet amd64 2:2.6-3 [130 kB]
Get:3 http://deb.debian.org/debian trixie/main amd64 telnet all 0.17+2.6-3 [43.3 kB]
Fetched 186 kB in 0s (3629 kB/s)         
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 79, <STDIN> line 3.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC entries checked: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.40.1 /usr/local/share/perl/5.40.1 /usr/lib/x86_64-linux-gnu/perl5/5.40 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.40 /usr/share/perl/5.40 /usr/local/lib/site_perl) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 8, <STDIN> line 3.)
debconf: falling back to frontend: Teletype
Selecting previously unselected package netbase.
(Reading database ... 6699 files and directories currently installed.)
Preparing to unpack .../archives/netbase_6.5_all.deb ...
Unpacking netbase (6.5) ...
Selecting previously unselected package inetutils-telnet.
Preparing to unpack .../inetutils-telnet_2%3a2.6-3_amd64.deb ...
Unpacking inetutils-telnet (2:2.6-3) ...
Selecting previously unselected package telnet.
Preparing to unpack .../telnet_0.17+2.6-3_all.deb ...
Unpacking telnet (0.17+2.6-3) ...
Setting up netbase (6.5) ...
Setting up inetutils-telnet (2:2.6-3) ...
update-alternatives: using /usr/bin/inetutils-telnet to provide /usr/bin/telnet (telnet) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/man1/telnet.1.gz because associated file /usr/share/man/man1/inetutils-telnet.1.gz (of link group telnet) doesn't exist
Setting up telnet (0.17+2.6-3) ...
root@frontend-797f589899-qpsg8:/# 
root@frontend-797f589899-qpsg8:/# telnet db 3306
Trying 10.102.45.242...
Connected to db.
Escape character is '^]'.
I
9.5.0   ]{dmU6EMb|}Slycaching_sha2_password
^CConnection closed by foreign host.
root@frontend-797f589899-qpsg8:/# exit
exit
controlplane:~$

## backend

controlplane:~$ kubectl exec -it backend-6567f6946f-ttrgp   -- bash 
root@backend-6567f6946f-ttrgp:/# apt update 
Get:1 http://deb.debian.org/debian trixie InRelease [140 kB]
Get:2 http://deb.debian.org/debian trixie-updates InRelease [47.3 kB]
Get:3 http://deb.debian.org/debian-security trixie-security InRelease [43.4 kB]
Get:4 http://deb.debian.org/debian trixie/main amd64 Packages [9670 kB]
Get:5 http://deb.debian.org/debian trixie-updates/main amd64 Packages [5412 B]
Get:6 http://deb.debian.org/debian-security trixie-security/main amd64 Packages [93.7 kB]
Fetched 10.0 MB in 1s (8118 kB/s)                          
All packages are up to date.    
root@backend-6567f6946f-ttrgp:/# apt install telnet
Installing:                     
  telnet

Installing dependencies:
  inetutils-telnet  netbase

Summary:
  Upgrading: 0, Installing: 3, Removing: 0, Not Upgrading: 0
  Download size: 186 kB
  Space needed: 377 kB / 11.0 GB available

Continue? [Y/n] y
Get:1 http://deb.debian.org/debian trixie/main amd64 netbase all 6.5 [12.4 kB]
Get:2 http://deb.debian.org/debian trixie/main amd64 inetutils-telnet amd64 2:2.6-3 [130 kB]
Get:3 http://deb.debian.org/debian trixie/main amd64 telnet all 0.17+2.6-3 [43.3 kB]
Fetched 186 kB in 0s (3544 kB/s)      
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 79, <STDIN> line 3.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC entries checked: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.40.1 /usr/local/share/perl/5.40.1 /usr/lib/x86_64-linux-gnu/perl5/5.40 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.40 /usr/share/perl/5.40 /usr/local/lib/site_perl) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 8, <STDIN> line 3.)
debconf: falling back to frontend: Teletype
Selecting previously unselected package netbase.
(Reading database ... 6699 files and directories currently installed.)
Preparing to unpack .../archives/netbase_6.5_all.deb ...
Unpacking netbase (6.5) ...
Selecting previously unselected package inetutils-telnet.
Preparing to unpack .../inetutils-telnet_2%3a2.6-3_amd64.deb ...
Unpacking inetutils-telnet (2:2.6-3) ...
Selecting previously unselected package telnet.
Preparing to unpack .../telnet_0.17+2.6-3_all.deb ...
Unpacking telnet (0.17+2.6-3) ...
Setting up netbase (6.5) ...
Setting up inetutils-telnet (2:2.6-3) ...
update-alternatives: using /usr/bin/inetutils-telnet to provide /usr/bin/telnet (telnet) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/man1/telnet.1.gz because associated file /usr/share/man/man1/inetutils-telnet.1.gz (of link group telnet) doesn't exist
Setting up telnet (0.17+2.6-3) ...
root@backend-6567f6946f-ttrgp:/# telnet db 3306
Trying 10.102.45.242...
Connected to db.
Escape character is '^]'.
I
9.5.0
BSr#GmCv{caching_sha2_password^CConnection closed by foreign host.
root@backend-6567f6946f-ttrgp:/# 
root@backend-6567f6946f-ttrgp:/# 
root@backend-6567f6946f-ttrgp:/# exit
exit
controlplane:~$

## network-policy

controlplane:~$ vi network-policy.yaml
controlplane:~$ cat network-policy.yaml 
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-test
spec:
  podSelector:
    matchLabels:
      name: mysql
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: backend
    ports:
    - port: 3306
controlplane:~$ kubectl apply -f network-policy.yaml 
networkpolicy.networking.k8s.io/db-test created
controlplane:~$ kubectl get networkpolicy
NAME      POD-SELECTOR   AGE
db-test   name=mysql     28s

## after applying network policy , frontend is not able to connect to db but backend still able to connect to db 

controlplane:~$ kubectl exec -it frontend-797f589899-qpsg8 -- bash 
root@frontend-797f589899-qpsg8:/# telnet db 3306
Trying 10.102.45.242...
^C
root@frontend-797f589899-qpsg8:/# exit
exit
command terminated with exit code 130
controlplane:~$

controlplane:~$ kubectl exec -it backend-6567f6946f-ttrgp   -- bash 
root@backend-6567f6946f-ttrgp:/# telnet db 3306 
Trying 10.102.45.242...
Connected to db.
Escape character is '^]'.
I
9.5.0
     ;}aat:4_woo#qmtlP,caching_sha2_password^CConnection closed by foreign host.
root@backend-6567f6946f-ttrgp:/# 
root@backend-6567f6946f-ttrgp:/# exit
exit

```
   
9. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
10. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
