# OAI-5G

## Pre-requisite

**Install**

k8s cluster (having RBAC)<br />
Multus-CNI<br />
Helm spray<br />

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

## Configure Helm Charts
```bash
cd oai-cn5g-fed/charts/oai-5g-core/oai-5g-basic
ls
sudo vi values.yaml
```

Update hostInterface: '' field with your interface of host machine (eth0)

## Update entries in database

Configurable parameters for all the network functions check, the PLMN, DNN and subscriber information in mysql database

```bash
sudo vi oai-cn5g-fed/charts/oai-5g-core/mysql/initialization/oai_db-basic.sql
```

**NOTE:** Enter these values to above

```bash
# Add a new or edit existing entries after AuthenticationSubscription table
INSERT INTO `AuthenticationSubscription` (`ueid`, `authenticationMethod`, `encPermanentKey`, `protectionParameterId`, `sequenceNumber`, `authenticationManagementField`, `algorithmId`, `encOpcKey`, `encTopcKey`, `vectorGenerationInHss`, `n5gcAuthMethod`, `rgAuthenticationInd`, `supi`) VALUES
('208990100001124', '5G_AKA', 'fec86ba6eb707ed08905757b1bb44b8f', 'fec86ba6eb707ed08905757b1bb44b8f', '{\"sqn\": \"000000000020\", \"sqnScheme\": \"NON_TIME_BASED\", \"lastIndexes\": {\"ausf\": 0}}', '8000', 'milenage', 'c42449363bbad02b66d16bc975d77cc1', NULL, NULL, NULL, NULL, '208990100001124');
# Add the PDN/DNN information after SessionManagementSubscriptionData table
# To assign a static ip-address use the below entry
INSERT INTO `SessionManagementSubscriptionData` (`ueid`, `servingPlmnid`, `singleNssai`, `dnnConfigurations`) VALUES 
('208990100001124', '20899', '{\"sst\": 1, \"sd\": \"10203\"}','{\"oai\":{\"pduSessionTypes\":{ \"defaultSessionType\": \"IPV4\"},\"sscModes\": {\"defaultSscMode\": \"SSC_MODE_1\"},\"5gQosProfile\": {\"5qi\": 6,\"arp\":{\"priorityLevel\": 1,\"preemptCap\": \"NOT_PREEMPT\",\"preemptVuln\":\"NOT_PREEMPTABLE\"},\"priorityLevel\":1},\"sessionAmbr\":{\"uplink\":\"100Mbps\", \"downlink\":\"100Mbps\"},\"staticIpAddress\":[{\"ipv4Addr\": \"12.1.1.85\"}]}}');
INSERT INTO `SessionManagementSubscriptionData` (`ueid`, `servingPlmnid`, `singleNssai`, `dnnConfigurations`) VALUES 
('208990100001125', '20899', '{\"sst\": 1, \"sd\": \"10203\"}','{\"oai\":{\"pduSessionTypes\":{ \"defaultSessionType\": \"IPV4\"},\"sscModes\": {\"defaultSscMode\": \"SSC_MODE_1\"},\"5gQosProfile\": {\"5qi\": 6,\"arp\":{\"priorityLevel\": 1,\"preemptCap\": \"NOT_PREEMPT\",\"preemptVuln\":\"NOT_PREEMPTABLE\"},\"priorityLevel\":1},\"sessionAmbr\":{\"uplink\":\"100Mbps\", \"downlink\":\"100Mbps\"}}}');
```

Once the charts are configured perform helm dependency update inside the chart repository
```bash
cd charts/oai-5g-core/oai-5g-basic
helm dependency update
```

## Deploy 
```bash
cd oai-cn5g-fed/charts/oai-5g-core/oai-5g-basic
helm spray .
```
