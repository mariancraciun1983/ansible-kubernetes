<h1 align="center">Ansible Kubernetes Cluster Role</h1>
<br />

<div align="center">
  <a href="https://travis-ci.com/mariancraciun1983/ansible-rabbitmq-ha">
    <img src="https://travis-ci.com/mariancraciun1983/ansible-rabbitmq-ha.svg?branch=master" alt="Build Status" />
  </a>
  <a href="https://galaxy.ansible.com/mariancraciun1983/rabbitmq_ha">
    <img src="https://img.shields.io/ansible/role/51705" alt="Ansible Galaxy" />
  </a>
  <a href="https://galaxy.ansible.com/mariancraciun1983/rabbitmq_ha">
    <img src="https://img.shields.io/ansible/quality/51705" alt="Ansible Quality Score" />
  </a>
  <a href="https://opensource.org/licenses/MIT">
    <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="MIT License" />
  </a>
</div>
<br />



## Introduction

  This role will install Kubernetes and provision a cluster of one master and multiple workers. In addition you can also create multiple admin users.


## Configuration example

For a single node with any number of worker nodes

```yaml
group_vars:
  all:
    k8s_cluster_endpoint: https://master.s-r-v.net:6443
    k8s_cluster_sans:
        - k8s.example.com
        - master.example.com
        - master1.example.com
host_vars:
  master1:
    k8s_role: master
  worker1:
    k8s_role: worker
  worker2:
    k8s_role: worker
```

Ignoring preflight errors and extra args

```yaml
# this is useful to ignore the swap and other checks
k8s_kubelet_extra_args: "--fail-swap-on=false --cgroup-driver=cgroupfs"
k8s_kubeinit_extra_args: "--ignore-preflight-errors=all"
k8s_kubejoin_extra_args: "--ignore-preflight-errors=all"
# or turn off the swap
# k8s_config_swapoff: true
```

Secure your kubernetes with internal IPs
Note that this will cause the K8s api to bind to local ip,
so a LB solution should give you access from the outside.
```yaml
group_vars:
  all:
    k8s_use_internal_ip: true
    # external endpoing (ex: LB(16443) -> K8s Api(6443))
    k8s_cluster_endpoint: https://master.s-r-v.net:16443
host_vars:
  master1:
    k8s_role: master
    internal_ip: 10.0.0.1
  worker1:
    k8s_role: worker
    internal_ip: 10.0.0.2
  worker2:
    k8s_role: worker
    internal_ip: 10.0.0.3
```

Create full priviledge users by generating the csr/cert and kubeconfig in files/k8s/users/*username*.

```yaml
# will create files/k8s/users/marian/kubeconfig
ansible-playbook playbook.yml -i inventory -t user -e k8s_new_user_name=marian
# will create files/k8s/users/john/kubeconfig
ansible-playbook playbook.yml -i inventory -t user -e k8s_new_user_name=john
```

## Testing

Molecule with docker is being used.

Running the tests:
```bash
pipenv install
pipenv run molecule test
```

## TODO

Allow adding more than 1 master.


## License

MIT License

