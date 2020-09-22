How to build a 3 node Docker Swarm Cluster?

Step 1: Set up the Nodes
- Depending on your environment, get 3 nodes (Linux preffered) with Docker engine installed.
- I'm using vmware Workstation to build my infrastructure with 3 CentOS 7 running Docker Engine.
- Seup the hostname with below commands.
//
[root@localhost ~]# hostnamectl set-hostname node-01
[root@localhost ~]# hostnamectl
[root@localhost ~]# systemctl reboot
//
- To install Docker on CentOS, follow below commands. You must install docker on all available nodes.

//
[root@node-02 ~]# yum update -y
[root@node-02 ~]# yum install -y yum-utils
[root@node-02 ~]# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
[root@node-02 ~]# yum install -y docker-ce docker-ce-cli containerd.io -y
[root@node-02 ~]# systemctl start docker
[root@node-02 ~]# chkconfig docker on
//
- Make sure you get below output for the 'docker version' command.
//
[root@node-02 ~]# docker version
Client: Docker Engine - Community
 Version:           19.03.13
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        4484c46d9d
 Built:             Wed Sep 16 17:03:45 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.13
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       4484c46d9d
  Built:            Wed Sep 16 17:02:21 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.3.7
  GitCommit:        8fba4e9a7d01810a393d5d25a3621dc101981175
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
[root@node-02 ~]# 
//

Step 2: Enable Docker Swarm
- Promote the node 1 as Master Swarm node using below command.
//
[root@node-01 ~]# docker swarm init
//

Step 3: Open Swarm ports on Master Node (node-01)

Swarm Manager
- TCP	2377
- TCP	7946
- UDP	7946
- UDP	4789

//
[root@node-01 ~]# firewall-cmd --zone=public --add-port=2377/tcp --permanent
[root@node-01 ~]# firewall-cmd --zone=public --add-port=7946/tcp --permanent
[root@node-01 ~]# firewall-cmd --zone=public --add-port=7946/udp --permanent
[root@node-01 ~]# firewall-cmd --zone=public --add-port=4789/udp --permanent
[root@node-01 ~]# firewall-cmd --reload
//

Swarm Workers
- TCP	7946
- UDP	7946
- UDP	4789
//
[root@node-02 ~]# firewall-cmd --zone=public --add-port=7946/tcp --permanent
[root@node-02 ~]# firewall-cmd --zone=public --add-port=7946/udp --permanent
[root@node-02 ~]# firewall-cmd --zone=public --add-port=4789/udp --permanent
[root@node-02 ~]# firewall-cmd --reload
//

- Verify: Make sure you have those ports are listed in the 'netstat -lntu' output.
//
[root@node-01 ~]# netstat -lntu
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.1:45763         0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN     
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp6       0      0 :::2377                 :::*                    LISTEN     
tcp6       0      0 :::7946                 :::*                    LISTEN     
tcp6       0      0 :::111                  :::*                    LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
tcp6       0      0 ::1:631                 :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
udp        0      0 0.0.0.0:4789            0.0.0.0:*                          
udp        0      0 0.0.0.0:54050           0.0.0.0:*                          
udp        0      0 0.0.0.0:864             0.0.0.0:*                          
udp        0      0 192.168.122.1:53        0.0.0.0:*                          
udp        0      0 0.0.0.0:67              0.0.0.0:*                          
udp        0      0 0.0.0.0:111             0.0.0.0:*                          
udp        0      0 0.0.0.0:5353            0.0.0.0:*                          
udp6       0      0 :::7946                 :::*                               
udp6       0      0 :::864                  :::*                               
udp6       0      0 :::111                  :::*                               
[root@node-01 ~]# 
//

Step 4: Get worker Token from Manager Node (node-01)

//
[root@node-01 ~]# docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-32sfxjtn6wyk43p6cz6ldgd3al8xp7g2w2luxfqr9bxg76tubp-2ikbz6kc7sdm7h09jaajd5w2x 192.168.39.21:2377
//

Step 5: Add node-02 to the Swarm Cluster as Worker Node

//
[root@node-02 ~]# docker swarm join --token SWMTKN-1-32sfxjtn6wyk43p6cz6ldgd3al8xp7g2w2luxfqr9bxg76tubp-2ikbz6kc7sdm7h09jaajd5w2x 192.168.39.21:2377
This node joined a swarm as a worker.
//

- You can follow the same steps to add remaining Worker nodes to the Manager node

Step 6: Verify the Swarm Cluster

//
[root@node-01 ~]# docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
aq0coma41cyx7ovboo0nffjmd *   node-01             Ready               Active              Leader              19.03.12
ns6r6gjjev920p56bcqx1wtlv     node-02             Ready               Active                                  19.03.13
w8rdibuypligqxwcfmhi5bzgb     node-03             Ready               Active                                  19.03.13
[root@node-01 ~]# 
//

Optional: If you want to promote any Worker node as Manager node, use update command from the Manager. 

//
[root@node-01 ~]# docker node update --role manager node-02
//

Optional: To create Redundant Containers

//
[root@node-01 ~]# docker service create --name web-app --publish 80:80 --replicas 3 vpjaseem/httpd-custom-website
tjtoplrsmgkj3oqn6oo4ujjjx
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
//

//
[root@node-01 ~]# docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE                                  PORTS
a4vsenp86uo5        web-app             replicated          3/3                 vpjaseem/httpd-custom-website:latest   *:80->80/tcp
[root@node-01 ~]# 
[root@node-01 ~]# 
[root@node-01 ~]# 
[root@node-01 ~]# docker service ps web-app 
ID                  NAME                IMAGE                                  NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
r91ors383kew        web-app.1           vpjaseem/httpd-custom-website:latest   node-01             Running             Running 50 seconds ago                       
6ftg69o24rms        web-app.2           vpjaseem/httpd-custom-website:latest   node-02             Running             Running 51 seconds ago                       
e1bfsm47v7rc        web-app.3           vpjaseem/httpd-custom-website:latest   node-03             Running             Running 50 seconds ago                       
[root@node-01 ~]# 
//

Optional: To Increase the Replicas

[root@node-01 ~]#  docker service update web-app --replicas 5

Optional: Remove Services

//
[root@node-01 ~]# docker service rm web-app
//

