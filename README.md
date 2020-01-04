# This is an automation group of scripts for Kubernetes
<p> It is designed to work with Centos07 and Vagrant

## Main script
<p>Vagrantfile is the main script. From within it will call the other scripts with numbers</p> 

### Support and completion scripts
<p> These will carry out some adjustments to help correct the environment configuration files.</p>

 * 1_firewalld 
 * 2_sysctl
 * 3_add-kube-repo-install-kubeadm
 * 4_installdocker
 * 5_restart_Docker_kube

#### Note:

```
   '/etc/containerd/config.toml'
  ## Must have these lines, else it will not start
  disabled_plugins = ["cri"]
  plugins_cri_systemd_cgroup = ["true"]
  root = "/var/lib/containerd"

```
#### Note for debug and analysis:

This command is essential to analysing the relationship, between containerd & kubelet:

```

   eval $(kubelet docker-env -u)
   sudo lsblk 
   sudo docker ps --format 'table {{.Image}}t{{.Status}}'
   kubectl get events
   kubectl get service
   vagrant ssh node-1 -c 'sudo docker images'

```
