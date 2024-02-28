# Rancher

Rancher is an open source container management platform built for organizations that deploy containers in production. Rancher makes it easy to run Kubernetes everywhere, meet IT requirements, and empower DevOps teams.


Rqancher is now running on 

- ✅ DEV - https://dev-rancher.oci.diksha.gov.in
- ✅ PROD - https://10.90.12.71:8443/dashboard/home


## 🔧 How to Install

### DEV Environment

```bash 
ssh -i <SSH KEY> ubuntu@jenkins.oci.diksha.gov.in
sudo su jenkins
cd /var/lib/jenkins/secrets
ssh -i deployer_ssh_key ubuntu@10.50.18.57
cd /home/ubuntu/rancher
./rancher.sh
```
### 🐳 Docker

``` docker
docker run --privileged -d --restart=always \
  -p 80:80 -p 443:443 \
  -v ./rancher-data:/var/lib/rancher \
  -v ./certs:/etc/rancher/ssl \
  -e "ACME_DOMAIN=dev-rancher.oci.diksha.gov.in" \
  rancher/rancher
 ``` 

### SSL Certificates

 certificates are placed inside ```/home/ubuntu/rancher/certs```

### Using Rancher

To learn more about using Rancher, please refer to our [Rancher Documentation](https://ranchermanager.docs.rancher.com/v2.8).

