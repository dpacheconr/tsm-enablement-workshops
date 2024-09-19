# Self Host
Use these instructions to run the demo in your own environment rather than in a codesapce. This allows you to run for longer amongst other benefits. The demo requires Docker, minikube, kubectl and helm and the guidance is based on an Ubuntu AWS EC2 t2.xlarge instance. You may need to adjust the installation of the dependencies if using a different operating system
.

## Environment Setup (Ubuntu)

The following is a useful user data script for an ec2 Ubunutu launch template. You could also run these (as root) in your VM directly.

```
exec > >(tee /var/log/user-data.log|logger -t user-data -s 2>/dev/console) 2>&1

# ----> Install docker (https://docs.docker.com/engine/install/ubuntu/)

# Setup permissions for docker to be accessed by "ubuntu" user
sudo groupadd docker
sudo usermod -aG docker ubuntu

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install -y ca-certificates curl unzip
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin



# ----> Install minikube (https://minikube.sigs.k8s.io/docs/start/)
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
sudo -u ubuntu bash -c  "minikube start"



# ----> Install kubectl (https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl



# ----> Install Helm (https://helm.sh/docs/intro/install/)
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

echo -e "\n\n User data installation steps complete."
```

## Installing the demo

Once the environment is in place clone the repository, update browseragent.js and run the installer:
```
git clone https://github.com/newrelic-experimental/tsm-enablement-workshops.git
```

Update browseragent.js with the code snippet from the Application Settings of Browser UI in New Relic:
```
cd tsm-enablement-workshops/otel-astro-demo
vi browseragent.js

// Key sequence to edit and save in vi: 
//     press "i" 
//     then paste snippet ("CMD+V")
//     then press [ESCAPE]
//     then type ":wq" 
//     then press [ENTER KEY] 
```

Then run the installer. You will need your ingest license key:
```
./install_selfhosted.sh
```


# Feature flags
The demo uses feature flags to trigger perfromance degradation scenarios.  View the [feature flag instructions](flagd_intructions.md) to learn how to apply them.