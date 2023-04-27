---
title: Run Local SP Network
order: 2
---

This guide helps you to set up a local Greenfield Storage Provider network for testing
and other development related purposes.

## Recommended Prerequisites

The following lists the recommended hardware requirements:

* VPS running recent versions of Mac OS X, Linux, or Windows；
* 16 cores of CPU, 64 GB of memory(RAM);
* At least 100GB disk space for backend storage;
* 10GB+ SQL Database.

## Quickly setup local Greenfield Blockchain network

1. Build Greenfield Blockchain

```shell
git clone https://github.com/bnb-chain/greenfield.git
cd greenfield
make build
```

2. Start Greenfield Blockchain

```shell
# 1 validator and 7 storage providers
bash ./deployment/localup/localup.sh all 1 7
```

3. Export the keys of sps

```shell
bash ./deployment/localup/localup.sh export_sps 1 7

# result example
# {
#   "sp0": {
#     "OperatorAddr": "0x14539343413EB47899B0935287ab1111Df891d04",
#     "FundingAddr": "0x21c6ff21DD7012DE1CCf9055f2eB234A44a1d3fB",
#     "SealAddr": "0x8e424c6Db42Ad9A5d91b24e20b5f603eC70abbA3",
#     "ApprovalAddr": "0x7Aa5C8B50696f1D15B3A60d6629f7318c605bb4C",
#     "GcAddr": "0xfa238a4B262e1dc35c4970A2296A2444B956c9Ca",
#     "OperatorPrivKey": "ba6e97958d9c43d1ad54923eba99f8d59f54a0c66c78a5dcbc004c5c3ec72f8c",
#     "FundingPrivKey": "bd9d9e7823cd2dc7bc20f1b6676c3025cdda6cf5a8df9b04597fdff42c29af01",
#     "SealPrivKey": "aacd6b834627fdbc5de2bfdb1db31be0ea810a941854787653814c8040a9dd39",
#     "ApprovalPrivKey": "32108ed1a47c0af965824f84ac2162c029f347eec6d0988e642330b0ac264c85",
#     "GcPrivKey": "2fad16031b4fd9facb7dacda3da4ca4dd5f005f4166891bf9f7be13e02abb12d"
#   }
# }
```

## Setup local SP network

1. Compile SP can refer this [doc](./compile-dependences.md#compile-sp).

2. Generate localup env

Use the following instruction to generate template config files in different directories.

```shell
# The first time setup GEN_CONFIG_TEMPLATE=1, and the other time is 0.
# When equal to 1, the configuration template will be generated.
GEN_CONFIG_TEMPLATE=1
bash ./deployment/localup/localup.sh --reset ${GEN_CONFIG_TEMPLATE}
```

3. Overwrite db and sp info

Overwrite all sps' db and sp info according to the real environment.

```shell
ls deployment/localup/local_env/sp0
├── sp0
│   ├── config.toml   # generated template config file
│   ├── db.info       # generated db.info is a template file, you need to overwrite real db info
│   ├── gnfd-sp0      # gnfd-sp binary
│   └── sp.info       # generated sp.info is a template file, you need to overwrite real sp info
├── sp1
├── ...
```

**Note** In local mode, you don't need to modify config.toml. You just need to manually add db config to db.info and add sp config to sp.info. There will provide a tool to fill db.info and sp.info in the future.

```shell
[root@yourmachine sp0]# cat db.infocat db.info
#!/usr/bin/env bash
USER=""                     # database username
PWD=""                      # database password
ADDRESS=""                  # db endpoint, e.g. "localhost:3306"
DATABASE=""                 # database name. You can set this var to what you like, but to be different in different sp directories.

[root@locahost sp0]# cat sp.info
#!/usr/bin/env bash
SP_ENDPOINT=""              # gateway endpoint, e.g. "127.0.0.1:9033"
OPERATOR_ADDRESS=""         # OperatorAddr which is generated in setup local Greenfield blockchain step 3.
OPERATOR_PRIVATE_KEY=""     # OperatorPrivKey which is generated in setup local Greenfield blockchain step 3.
FUNDING_PRIVATE_KEY=""      # FundingPrivKey which is generated in setup local Greenfield blockchain step 3.
SEAL_PRIVATE_KEY=""         # SealPrivKey which is generated in setup local Greenfield blockchain step 3.
APPROVAL_PRIVATE_KEY=""     # ApprovalPrivKey which is generated in setup local Greenfield blockchain step 3.
```

4. Start Seven SPs

Make config.toml according to db.info, sp.info and start seven sps.

```shell
# In first time setup GEN_CONFIG_TEMPLATE=1, and the other time is 0.
# When equal to 1, the configuration template will be generated.
GEN_CONFIG_TEMPLATE=0
bash ./deployment/localup/localup.sh --reset ${GEN_CONFIG_TEMPLATE}
bash ./deployment/localup/localup.sh --start
```

The environment directory is as follows:

```shell
deployment/localup/local_env/
├── sp0
│   ├── config.toml    # real config
│   ├── data           # piecestore data directory
│   ├── db.info
│   ├── gnfd-sp0
│   ├── gnfd-sp.log    # gnfd-sp log file
│   ├── log.txt
│   └── sp.info
├── sp1
├── ...
```

5. Other supported commands

```shell
% bash ./deployment/localup/localup.sh --help
Usage: deployment/localup/localup.sh [option...] {help|reset|start|stop|print}

   --help                           display help info
   --reset $GEN_CONFIG_TEMPLATE     reset env, $GEN_CONFIG_TEMPLATE=0 or =1
   --start                          start storage providers
   --stop                           stop storage providers
   --print                          print sp local env work directory
```
