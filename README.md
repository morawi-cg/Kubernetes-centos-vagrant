# This is an automation group of scripts for Kubernetes
<p> It is designed to work with Centos07 and Vagrant

## Main script
<p>Vagrantfile is the main script. From within it will call the other scripts with numbers</p> 

### Support and completion scripts
<p> These will carry out some adjustments to help correct the environment configuration files.</p>

 *1_firewalld 
 *2_sysctl
 *3_add-kube-repo-install-kubeadm
 *4_installdocker
 *5_restart_Docker_kube
