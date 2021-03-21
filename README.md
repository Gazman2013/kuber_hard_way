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
>   --network kubernetes-the-hard-way \
>   --range 10.240.0.0/24
Created [https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/regions/europe-west1/subnetworks/kubernetes].
NAME        REGION        NETWORK                  RANGE
kubernetes  europe-west1  kubernetes-the-hard-way  10.240.0.0/24
13. gcloud compute firewall-rules create kubernetes-the-hard-way-allow-internal \
  --allow tcp,udp,icmp \
  --network kubernetes-the-hard-way \
  --source-ranges 10.240.0.0/24,10.200.0.0/16
14. gcloud compute firewall-rules create kubernetes-the-hard-way-allow-external \
>   --allow tcp:22,tcp:6443,icmp \
>   --network kubernetes-the-hard-way \
>   --source-ranges 0.0.0.0/0
Creating firewall...⠹Created [https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/global/firewalls/kubernetes-the-hard-way-allow-external].
Creating firewall...done.                                                                                         
NAME                                    NETWORK                  DIRECTION  PRIORITY  ALLOW                 DENY  DISABLED
kubernetes-the-hard-way-allow-external  kubernetes-the-hard-way  INGRESS    1000      tcp:22,tcp:6443,icmp        False
15. gcloud compute firewall-rules list --filter="network:kubernetes-the-hard-way"
NAME                                    NETWORK                  DIRECTION  PRIORITY  ALLOW                 DENY  DISABLED
kubernetes-the-hard-way-allow-external  kubernetes-the-hard-way  INGRESS    1000      tcp:22,tcp:6443,icmp        False
kubernetes-the-hard-way-allow-internal  kubernetes-the-hard-way  INGRESS    1000      tcp,udp,icmp                False

To show all fields of the firewall, please show in JSON format: --format=json
To show all fields in table format, please see the examples in --help.
16. gcloud compute addresses create kubernetes-the-hard-way \
>   --region $(gcloud config get-value compute/region)
Created [https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/regions/europe-west1/addresses/kubernetes-the-hard-way]
17. gcloud compute addresses list --filter="name=('kubernetes-the-hard-way')"
NAME                     ADDRESS/RANGE  TYPE      PURPOSE  NETWORK  REGION        SUBNET  STATUS
kubernetes-the-hard-way  34.77.232.254  EXTERNAL                    europe-west1          RESERVED
18. for i in 0 1 2; do
>   gcloud compute instances create controller-${i} \
>     --async \
>     --boot-disk-size 200GB \
>     --can-ip-forward \
>     --image-family ubuntu-2004-lts \
>     --image-project ubuntu-os-cloud \
>     --machine-type e2-standard-2 \
>     --private-network-ip 10.240.0.1${i} \
>     --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
>     --subnet kubernetes \
>     --tags kubernetes-the-hard-way,controller
> done
NOTE: The users will be charged for public IPs when VMs are created.
Instance creation in progress for [controller-0]: https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/zones/europe-west1-d/operations/operation-1616349213052-5be0fa0ad4e67-944b3bf4-8ff1c25f
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
NOTE: The users will be charged for public IPs when VMs are created.
Instance creation in progress for [controller-1]: https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/zones/europe-west1-d/operations/operation-1616349215458-5be0fa0d20329-27a5efe3-02d0187b
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
NOTE: The users will be charged for public IPs when VMs are created.
Instance creation in progress for [controller-2]: https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/zones/europe-west1-d/operations/operation-1616349217932-5be0fa0f7c560-f37cfd3d-e08dbd15
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
root@kuber:/home/kuber# 
18. for i in 0 1 2; do
>   gcloud compute instances create worker-${i} \
>     --async \
>     --boot-disk-size 200GB \
>     --can-ip-forward \
>     --image-family ubuntu-2004-lts \
>     --image-project ubuntu-os-cloud \
>     --machine-type e2-standard-2 \
>     --metadata pod-cidr=10.200.${i}.0/24 \
>     --private-network-ip 10.240.0.2${i} \
>     --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
>     --subnet kubernetes \
>     --tags kubernetes-the-hard-way,worker
> done
NOTE: The users will be charged for public IPs when VMs are created.
Instance creation in progress for [worker-0]: https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/zones/europe-west1-d/operations/operation-1616349350558-5be0fa8df7994-7da0b617-6407023a
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
NOTE: The users will be charged for public IPs when VMs are created.
Instance creation in progress for [worker-1]: https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/zones/europe-west1-d/operations/operation-1616349353047-5be0fa9057554-9080c7fa-e8ed6af1
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
NOTE: The users will be charged for public IPs when VMs are created.
Instance creation in progress for [worker-2]: https://www.googleapis.com/compute/v1/projects/tokyo-portal-304417/zones/europe-west1-d/operations/operation-1616349355419-5be0fa929a818-cea03e55-910b402f
Use [gcloud compute operations describe URI] command to check the status of the operation(s).
19. gcloud compute instances list --filter="tags.items=kubernetes-the-hard-way"
NAME          ZONE            MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
controller-0  europe-west1-d  e2-standard-2               10.240.0.10  34.78.239.183   RUNNING
controller-1  europe-west1-d  e2-standard-2               10.240.0.11  35.241.222.66   RUNNING
controller-2  europe-west1-d  e2-standard-2               10.240.0.12  35.187.191.244  RUNNING
worker-0      europe-west1-d  e2-standard-2               10.240.0.20  35.190.198.100  RUNNING
worker-1      europe-west1-d  e2-standard-2               10.240.0.21  35.195.241.169  RUNNING
worker-2      europe-west1-d  e2-standard-2               10.240.0.22  192.158.31.224  RUNNING
20. gcloud compute ssh controller-0
WARNING: The public SSH key file for gcloud does not exist.
WARNING: The private SSH key file for gcloud does not exist.
WARNING: You do not have an SSH key for gcloud.
WARNING: SSH keygen will be executed to generate a key.
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
21. in root@controller-0:~# exit
22. Provisioning a CA and Generating TLS Certificates
23. create certificates 
24. for instance in worker-0 worker-1 worker-2; do
>   gcloud compute scp ca.pem ${instance}-key.pem ${instance}.pem ${instance}:~/
> done
Warning: Permanently added 'compute.8830034352361059913' (ECDSA) to the list of known hosts.
ca.pem                                                                                                                                       100% 1318    12.1KB/s   00:00    
worker-0-key.pem                                                                                                                             100% 1679    15.4KB/s   00:00    
worker-0.pem                                                                                                                                 100% 1493    13.7KB/s   00:00    
Warning: Permanently added 'compute.2730611661629699654' (ECDSA) to the list of known hosts.
ca.pem                                                                                                                                       100% 1318    12.1KB/s   00:00    
worker-1-key.pem                                                                                                                             100% 1679    15.4KB/s   00:00    
worker-1.pem                                                                                                                                 100% 1493    13.7KB/s   00:00    
Warning: Permanently added 'compute.4360593454594143812' (ECDSA) to the list of known hosts.
ca.pem                                                                                                                                       100% 1318    12.1KB/s   00:00    
worker-2-key.pem                                                                                                                             100% 1679    15.3KB/s   00:00    
worker-2.pem                                                                                                                                 100% 1493    13.6KB/s   00:00    
25. 
