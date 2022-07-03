# OAI-5G

## Pre-requisite

Install

k8s cluster (having RBAC)< /br>
Multus-CNI

## Build Images
```bash
git clone  https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed.git
cd oai-cn5g-fed

# On an existing repository, resync to the last `master` commit

git fetch --prune
git checkout master
git rebase origin/master

# Synchronize all git submodules

./scripts/syncComponents.sh --nrf-branch develop --amf-branch develop \
                              --smf-branch develop --spgwu-tiny-branch develop \
                              --ausf-branch develop --udm-branch develop \
                              --udr-branch develop --upf-vpp-branch develop \
                              --nssf-branch develop

```

