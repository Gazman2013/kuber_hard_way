# kuber_hard_way
1  wget -q --show-progress --https-only --timestamping   https://storage.googleapis.com/kubernetes-the-hard-way
/cfssl/1.4.1/linux/cfssl   https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/linux/cfssljson
2  cfssl version
3  cfssl --version
4  chmod +x cfssl cfssljson
5  sudo mv cfssl cfssljson /usr/local/bin/
6  cfssl version
7  wget https://storage.googleapis.com/kubernetes-release/release/v1.18.6/bin/linux/amd64/kubectl
8  chmod +x kubectl
9  sudo mv kubectl /usr/local/bin/
10  kubectl version --client v1.18.6
