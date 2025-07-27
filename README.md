# Minikube Installer Playbook for RHEL 9

An opinionated Ansible playbook for installing and configuring [Minikube](https://minikube.sigs.k8s.io/) on Red Hat Enterprise Linux 9 (RHEL 9) or compatible distributions (e.g., Rocky Linux 9) using **Docker CE** as the container runtime.

This playbook:

- Installs Minikube binary and verify it is idempotent  
- Installs Docker Community Edition (Docker CE) with repository configuration  
- Ensures Docker service is running and enabled  
- Adds the specified user to the Docker group for proper permissions  
- Starts Minikube using the Docker driver as a non-root user  
- Optionally starts the Minikube Kubernetes dashboard and displays the access URL  
- Provides useful information for accessing the dashboard remotely via SSH tunneling  

## Table of Contents

- [Features](#features)  
- [Prerequisites](#prerequisites)  
- [Usage](#usage)  
- [Variables](#variables)  
- [How It Works](#how-it-works)  
- [Accessing the Dashboard](#accessing-the-dashboard)  
- [Troubleshooting](#troubleshooting)  
- [Contributing](#contributing)  
- [License](#license)  

## Features

- Idempotent download/install of Minikube binary with checksum verification  
- Automated Docker CE installation with proper repo setup on RHEL 9  
- User group management for seamless non-root Docker and Minikube usage  
- Minikube start and status verification  
- Dashboard start with asynchronous execution and URL output  
- Helpful post-install guidance for remote dashboard access  

## Prerequisites

- RHEL 9 or compatible OS (Rocky Linux 9, AlmaLinux 9, CentOS Stream 9)  
- SSH access to the target hosts  
- Python and Ansible installed on the control machine  
- The remote user account to run Minikube must exist and be SSH-accessible  
- Network access to Docker and Minikube repositories  

## Usage

1. **Clone this repository**

```
git clone https://github.com/yourusername/minikube-ansible-rhel9.git
cd minikube-ansible-rhel9
```

2. **Update the inventory file**

Specify your target hosts in your Ansible inventory.

3. **Set the non-root user**

Edit the playbook’s `non_root_user` variable to match the username on your target hosts that will run Minikube commands.

4. **Run the playbook**

```
ansible-playbook -i inventory install_minikube.yml -e "non_root_user=your_username"
```

## Variables

| Variable           | Default      | Description                                              |
|--------------------|--------------|----------------------------------------------------------|
| `minikube_version` | `v1.32.0`    | Version of Minikube binary to install                    |
| `non_root_user`    | _required_   | The user to run Minikube and Docker commands as          |

Change variables in the playbook or pass them as extra-vars during execution.

## How It Works

- The playbook installs required packages for Docker and repository management  
- Adds the official Docker CE repository and installs Docker packages  
- Starts and enables the Docker service  
- Adds the specified user to the `docker` group (requires logout/login or `newgrp docker` to take effect)  
- Downloads Minikube binary to `/usr/local/bin/minikube` with checksum validation  
- Checks Minikube cluster status and starts it if not running using the Docker driver as the non-root user  
- Starts Minikube dashboard asynchronously  

## Accessing the Dashboard

The playbook outputs the dashboard's local URL. However, by default, Minikube dashboard listens on `localhost` and is only accessible from the Minikube host.

To access remotely:

1. Set up an SSH tunnel from your local machine:
```
ssh -L 8001:127.0.0.1:<dashboard-port> your_username@minikube_host
```

2. Open your browser and go to:

Example:
http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/


Keep the SSH tunnel open to maintain connectivity.

## Troubleshooting

- **Docker permission denied:** Ensure the user is in the `docker` group, log out and log back in or run `newgrp docker`  
- **Minikube start fails:** Confirm Docker service is running, and try running `minikube start` manually as the user  
- **Dashboard URL not shown:** Make sure the asynchronous dashboard task has sufficient time to start  
- **Container runtime conflicts:** This playbook is designed for Docker CE; ensure Podman or others are not conflicting  

## Contributing

Contributions and improvements are welcome! Please fork the repository and submit pull requests for review.

## License

This project is licensed under the [MIT License](LICENSE).

## Author

Alberto Cortes — [rico_cebiche@yahoo.com](mailto:your.email@example.com) | [GitHub Profile](https://github.com/alcortes01)

---

**Enjoy your Minikube setup on RHEL 9 with Docker CE!**