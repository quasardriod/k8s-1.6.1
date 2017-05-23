# k8s-1.6.1 using weave over relay network

Weave network
-----------------------
1) After installing weave network in all nodes, each node will have one "weave" bridge with different network.
[root@rhel7-host ~]# ssh 192.168.2.112 -l stack ifconfig weave
weave     Link encap:Ethernet  HWaddr 4e:d4:5c:a3:f9:f1  
         inet addr:10.32.0.1  Bcast:0.0.0.0  Mask:255.240.0.0
         inet6 addr: fe80::4cd4:5cff:fea3:f9f1/64 Scope:Link

[root@rhel7-host ~]# ssh 192.168.2.113 -l stack ifconfig weave
weave     Link encap:Ethernet  HWaddr e2:30:41:48:e5:4e  
         inet addr:10.46.0.0  Bcast:0.0.0.0  Mask:255.240.0.0
         inet6 addr: fe80::e030:41ff:fe48:e54e/64 Scope:Link

[root@rhel7-host ~]# ssh 192.168.2.111 -l stack ifconfig weave
weave     Link encap:Ethernet  HWaddr 5e:9b:d1:b4:90:ed  
         inet addr:10.40.0.0  Bcast:0.0.0.0  Mask:255.240.0.0
         inet6 addr: fe80::5c9b:d1ff:feb4:90ed/64 Scope:Link


2) Without any pod, weave network will have one interface "vethwe-bridge" attached to it.
[root@rhel7-host ~]# ssh 192.168.2.112 -l stack brctl show
bridge name	bridge id		STP enabled	interfaces
docker0		8000.02429804a337	no		
weave		8000.4ed45ca3f9f1	no		vethwe-bridge

3) Weave interfaces are attached to pods. Each pod will have one IP from weave network.

4) Created Nginx pods
stack@kube-master:~$ kubectl get pods -o wide
NAME                                READY     STATUS    RESTARTS   AGE       IP          NODE
nginx-deployment-3285060500-36tnm   1/1       Running   0          33s       10.46.0.2   kube-node2
nginx-deployment-3285060500-49pm7   1/1       Running   0          33s       10.32.0.2   kube-node1
nginx-deployment-3285060500-lc7v7   1/1       Running   0          50s       10.32.0.3   kube-node1
nginx-deployment-3285060500-tfr3l   1/1       Running   0          47s       10.46.0.3   kube-node2

5) Two additional interfaces created for each pods in both nodes.
Note: host 192.168.2.113 has additional interface for kube-dns pod
[root@rhel7-host ~]# ssh 192.168.2.112 -l stack "brctl show"
bridge name	bridge id		STP enabled	interfaces
docker0		8000.02429804a337	no		
weave		8000.4ed45ca3f9f1	no		 vethwe-bridge
							                   vethwepl6ed7ab7
							                   vethweplf40dbf8
[root@rhel7-host ~]# ssh 192.168.2.113 -l stack "brctl show"
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242ccda4a50	no		
weave		8000.e2304148e54e	no		 vethwe-bridge
							                   vethwepl0c2e699
							                   vethwepl6ef7542
							                   vethweplcd02b5e
