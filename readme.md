# K3s Deployment

This directory contains Ansible playbooks and configuration files for deploying a single-node K3s Kubernetes cluster on Enterprise Linux 9 (EL9) systems.

## Files

- **`k3s.yml`** - Main Ansible playbook for installing and configuring K3s
- **`inventory`** - Ansible inventory file specifying localhost as the target
- **`readme.md`** - This documentation file

## Prerequisites

- Enterprise Linux 9 (RHEL, CentOS Stream, Rocky Linux, AlmaLinux, etc.)
- Ansible installed on the local machine
- Root/sudo access on the target system
- Internet connectivity for downloading K3s

## What This Playbook Does

The playbook performs the following tasks:

1. **Package Installation** - Installs required base packages (curl, iptables, firewalld)
2. **Firewall Configuration** - Opens necessary ports for K3s components:
   - 6443/tcp (kube-apiserver)
   - 10250/tcp (kubelet metrics)
   - 2379-2380/tcp (etcd)
   - 30000-32767/tcp (NodePort range)
   - 80/tcp and 443/tcp (ingress traffic)
3. **K3s Installation** - Downloads and installs K3s version v1.30.2+k3s1
4. **Service Configuration** - Configures K3s with disabled servicelb and traefik
5. **Verification** - Waits for the node to become Ready and displays status

## Usage

Run the playbook using the following command:

```bash
ansible-playbook -i inventory k3s.yml
```

Or using the localhost connection directly:

```bash
ansible-playbook -i 'localhost,' -c local k3s.yml
```

## Configuration

The playbook uses the following default configuration:

- **K3s Version**: v1.30.2+k3s1 (pinned version)
- **Extra Arguments**: `--write-kubeconfig-mode 644 --disable servicelb --disable traefik`
- **Config Directory**: `/etc/rancher/k3s`
- **Kubeconfig**: Written with 644 permissions for easy access

## Post-Installation

After successful installation:

1. K3s will be running as a systemd service
2. kubectl is available at `/usr/local/bin/k3s kubectl`
3. Kubeconfig is located at `/etc/rancher/k3s/k3s.yaml`
4. The cluster is ready for deploying applications

## Accessing the Cluster

You can interact with the cluster using:

```bash
# Using the k3s binary
/usr/local/bin/k3s kubectl get nodes

# Or set up kubectl alias
alias kubectl='/usr/local/bin/k3s kubectl'
kubectl get nodes
```

## Notes

- This is a single-node cluster suitable for development and testing
- ServiceLB and Traefik are disabled by default
- The NodePort range is configured as 30000-32767
- Firewall rules are automatically configured for required ports
