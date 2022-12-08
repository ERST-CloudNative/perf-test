1. 部署iperf3
```
(ap-tokyo-1)$ kc apply -f iper3.yaml
(ap-tokyo-1)$ kc get pods -o wide 
NAME                                        READY   STATUS    RESTARTS   AGE   IP           NODE         NOMINATED NODE   READINESS GATES
iperf3-clients-6qcmd                        1/1     Running   0          19s   10.0.1.40    10.0.1.78    <none>           <none>
iperf3-clients-ng8pt                        1/1     Running   0          19s   10.0.1.230   10.0.1.234   <none>           <none>
iperf3-clients-ngstb                        1/1     Running   0          19s   10.0.1.97    10.0.1.173   <none>           <none>
iperf3-server-deployment-85b74f7db6-mfvx8   1/1     Running   0          19s   10.0.1.71    10.0.1.173   <none>           <none>
```

2. 执行iperf测试

```
(ap-tokyo-1)$ kc exec iperf3-clients-6qcmd -i -t -- /bin/sh
# iperf3 -c 10.0.1.71
Connecting to host 10.0.1.71, port 5201
[  5] local 10.0.1.40 port 34430 connected to 10.0.1.71 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   253 MBytes  2.13 Gbits/sec   46   1.54 MBytes       
[  5]   1.00-2.00   sec   249 MBytes  2.09 Gbits/sec    0   2.16 MBytes       
[  5]   2.00-3.00   sec   250 MBytes  2.10 Gbits/sec    0   2.64 MBytes       
[  5]   3.00-4.00   sec   249 MBytes  2.09 Gbits/sec    0   3.03 MBytes       
[  5]   4.00-5.00   sec   249 MBytes  2.09 Gbits/sec    0   3.39 MBytes       
[  5]   5.00-6.00   sec   249 MBytes  2.09 Gbits/sec    0   3.70 MBytes       
[  5]   6.00-7.00   sec   250 MBytes  2.10 Gbits/sec    0   4.00 MBytes       
[  5]   7.00-8.00   sec   249 MBytes  2.09 Gbits/sec    0   4.28 MBytes       
[  5]   8.00-9.00   sec   248 MBytes  2.08 Gbits/sec    0   4.53 MBytes       
[  5]   9.00-10.00  sec   249 MBytes  2.09 Gbits/sec    0   4.77 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  2.44 GBytes  2.09 Gbits/sec   46             sender
[  5]   0.00-10.02  sec  2.43 GBytes  2.08 Gbits/sec                  receiver

iperf Done.
```

3. 部署ping测试的镜像

```
(ap-tokyo-1)$ kubectl run test01 --image=centos:latest --command -- sleep 36000
pod/test01 created

(ap-tokyo-1)$ kc get pods -o wide
NAME                                        READY   STATUS    RESTARTS   AGE     IP           NODE         NOMINATED NODE   READINESS GATES
iperf3-clients-6qcmd                        1/1     Running   0          7m29s   10.0.1.40    10.0.1.78    <none>           <none>
iperf3-clients-ng8pt                        1/1     Running   0          7m29s   10.0.1.230   10.0.1.234   <none>           <none>
iperf3-clients-ngstb                        1/1     Running   0          7m29s   10.0.1.97    10.0.1.173   <none>           <none>
iperf3-server-deployment-85b74f7db6-mfvx8   1/1     Running   0          7m29s   10.0.1.71    10.0.1.173   <none>           <none>
test01                                      1/1     Running   0          15s     10.0.1.200   10.0.1.78    <none>           <none>
```

4. 开始ping测试

```
(ap-tokyo-1)$ kc exec test01 -it -- ping -c 100 10.0.1.71 >> ping_native.txt
```

5. 查看ping测试结果

```
 (ap-tokyo-1)$ tail -n 10 -f ping_native.txt 
64 bytes from 10.0.1.71: icmp_seq=95 ttl=60 time=0.496 ms
64 bytes from 10.0.1.71: icmp_seq=96 ttl=60 time=0.423 ms
64 bytes from 10.0.1.71: icmp_seq=97 ttl=60 time=0.432 ms
64 bytes from 10.0.1.71: icmp_seq=98 ttl=60 time=0.436 ms
64 bytes from 10.0.1.71: icmp_seq=99 ttl=60 time=0.432 ms
64 bytes from 10.0.1.71: icmp_seq=100 ttl=60 time=0.594 ms

--- 10.0.1.71 ping statistics ---
100 packets transmitted, 100 received, 0% packet loss, time 495ms
rtt min/avg/max/mdev = 0.357/0.524/0.771/0.079 ms
```
