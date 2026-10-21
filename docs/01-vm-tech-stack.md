# Install the tech stack

## Ready Kernel
To be able to run docker on Rocky-Linux 10, modules from the kernel extras are needed.
To get all the prerequisites for the kernel run:
uname -r
dnf upgrade --refresh -y
dnf install -y kernel-modules-extra
reboot

## Docker install 
dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
dnf -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
systemctl enable --now docker

## Deploy containers
dnf install git
git clone https://github.com/Adm-Silvan/Eco-Hack-DB.git
docker compose up -d --build