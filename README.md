# Large Scale Computing - Lab 6 – Kubernetes


Commands were executed in WSL 2 (Linux Ubuntu).
You should download:
- aws cli:
  ```bash
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```
- kubectl:
  ```bash
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
  ```
- helm:
  ```bash
  curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
  chmod 700 get_helm.sh
  ./get_helm.sh
  ```

Commands to create a cluster and create and add a node group on Amazon Elastic Kubernetes Service (EKS):
- ```bash
  aws eks create-cluster \
    --name lsc-lab-cluster \
    --region us-east-1 \
    --resources-vpc-config subnetIds=subnet-0211465f62c3523bb,subnet-02d9229142ad230e2,securityGroupIds=sg-093f2a8580b312d45 \
    --role-arn arn:aws:iam::305245345721:role/LabRole
  ```
- ```bash
  aws eks create-nodegroup \
    --cluster-name lsc-lab-cluster \
    --nodegroup-name lsc-lab-nodegroup \
    --node-role arn:aws:iam::305245345721:role/LabRole \
    --subnets "subnet-0211465f62c3523bb" "subnet-02d9229142ad230e2" \
    --scaling-config minSize=1,maxSize=3,desiredSize=1 \
    --region us-east-1 \
    --instance-types 't3.medium' \
    --ami-type AL2_x86_64 \
    --disk-size 20
  ```
- ```bash
  aws eks --region us-east-1 update-kubeconfig --name lsc-lab-cluster
  ```

To view detailed information about a cluster, use:
```bash
aws eks  describe-cluster --region us-east-1 --name lsc-lab-cluster
```

Installing NFS server and provisioner in cluster using helm:
```bash
helm repo add nfs-ganesha-server-and-external-provisioner https://kubernetes-sigs.github.io/nfs-ganesha-server-and-external-provisioner/
helm repo update
helm install nfs-server nfs-ganesha-server-and-external-provisioner/nfs-server-provisioner -f nfs-values-data.yaml
```



  
