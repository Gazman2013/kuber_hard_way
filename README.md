# kuber_hard_way
1.  wget -q --show-progress --https-only --timestamping   https://storage.googleapis.com/kubernetes-the-hard-way
/cfssl/1.4.1/linux/cfssl   https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/linux/cfssljson
2.  cfssl version
3.  cfssl --version
4.  chmod +x cfssl cfssljson
5.  sudo mv cfssl cfssljson /usr/local/bin/
6.  cfssl version
7.  wget https://storage.googleapis.com/kubernetes-release/release/v1.18.6/bin/linux/amd64/kubectl
8.  chmod +x kubectl
9.  sudo mv kubectl /usr/local/bin/
10.  kubectl version --client v1.18.6
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.6", GitCommit:"dff82dc0de47299ab66c83c626e08b
245ab19037", GitTreeState:"clean", BuildDate:"2020-07-15T16:58:53Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:
"linux/amd64"}
11. gcloud compute networks create kubernetes-the-hard-way --subnet-mode custom
Created [https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/global/networks/kubernetes-the-hard-way].
NAME                     SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
kubernetes-the-hard-way  CUSTOM       REGIONAL
Instances on this network will not be reachable until firewall rules
are created. As an example, you can allow all internal traffic between
instances as well as SSH, RDP, and ICMP by running:
$ gcloud compute firewall-rules create <FIREWALL_NAME> --network kubernetes-the-hard-way --allow tcp,udp,icmp --source-ranges <IP_RANGE>
$ gcloud compute firewall-rules create <FIREWALL_NAME> --network kubernetes-the-hard-way --allow tcp:22,tcp:3389,icmp
12. gcloud compute networks subnets create kubernetes \
  --network kubernetes-the-hard-way \
  --range 10.240.0.0/24
13. 
