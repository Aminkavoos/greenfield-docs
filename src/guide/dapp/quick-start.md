---
title: Quick Start
order: 2
---

# Quick Start

In this guide, we will walk you through the process of data permission management using the BSC smart contract as a simple
showcase of cross chain program-ability of Greenfield.

## Prerequisites

Before starting, make sure you have the following tools installed:
- [gnfd-cmd](https://github.com/bnb-chain/greenfield-cmd)
- [gnfd-contract](https://github.com/bnb-chain/greenfield-contracts)

Please follow the readme of the above two repositories to install the tools and configure the environment.

Ensure you get an account that get funds on both BSC and Greenfield network.

### Steps

In the following example, Account A(0x0fEd1aDD48b497d619EF50160f9135c6E221D5F0) will grant Account B(0x3bD70E10D71C6E882E3C1809d26a310d793646eB)
the access to his private file through BSC contract.

Before starting, please make sure you have the config.toml file in the current directory. 
The content of the config.toml is as follows:

```toml
endpoint = "https://gnfd-testnet-sp-1.bnbchain.org"
grpcAddr = "gnfd-testnet-fullnode-cosmos-us.bnbchain.org:9090"
chainId = "greenfield_5600-1"

## Please replace the ${PrivateKey} with your own Account A's private key
privateKey = "${PrivateKey}"
```

1. Prepare environment

```shell
$ export AccountA=0x0fEd1aDD48b497d619EF50160f9135c6E221D5F0
$ export AccountB=0x3bD70E10D71C6E882E3C1809d26a310d793646eB
```

2. Create a temporary file `story.txt`

```shell
$ echo "this is a fun story" > story.txt 
```

3. Create a bucket named `funbucket`.

```shell
$ gnfd-cmd -c config.toml storage make-bucket gnfd://funbucket
```

4. Create a private object named `story.txt` in the bucket `funbucket`.

```shell
$ gnfd-cmd -c config.toml storage put  --contentType "text/xml" --visibility private ./story.txt  gnfd://funbucket/story.txt
```

5. Create a group named `fungroup`.

```shell
$ gnfd-cmd -c config.toml group make-group gnfd://fungroup
create group: fungroup succ, txn hash:17B6AE2C8D30B6D6EEABEE81DB8B37CF735655E9087CB02DC98EFF1DCA9FBE3A, group id: 136 
```

The console will return the id of the group, which is `136` in this case.

6. Bind the group `fungroup` to the object `story.txt`.

```shell
## Example, replace the ${GroupId} with the group id you get in the previous step
$ export GroupId=136
$ gnfd-cmd -c config.toml permission put-obj-policy --groupId ${GroupId}  --actions get  gnfd://funbucket/story.txt   
```

6. Mirror the group to BSC network.

```shell
## Example, replace the ${GroupId} with the group id you get in the previous step
$ gnfd-cmd -c config.toml crosschain mirror --resource group --id  ${GroupId} 
```

7. Change the `PrivateKey` of config.toml to AccountB, and try to access the file through AccountB.
    
```shell
## Example
$ gnfd-cmd -c config.toml group head-member  --groupOwner ${AccountA} --headMember ${AccountB} gnfd://fungroup
the user does not exist in the group
$ gnfd-cmd -c config.toml storage get gnfd://funbucket/story.txt ./story.txt
run command error: statusCode 403 : code : AccessDenied  (Message: Access Denied)
```

It turns out that AccountB is not permitted to access the file, which is expected.

8. Clone the [gnfd-contract](https://github.com/bnb-chain/greenfield-contracts) repository and install the dependencies.


9. Grant the access to Account B through the contract.

```shell
### Example
export RPC_TEST=https://gnfd-bsc-testnet-dataseed1.bnbchain.org 
$ forge script foundry-scripts/GroupHub.s.sol:GroupHubScript \
--sig "updateGroup(address operator, uint256 groupId, address member)" \
${AccountA} ${GroupId} ${AccountB} \
-f https://greenfield-bsc-testnet-ap.nodereal.io/ \
--private-key 148748590a8b83dxxxxxxxxxxxxxxxxx \
--legacy \
--broadcast
```

9. Wait 30 seconds, and try to access the file through AccountB again.
```shell
## Example
$ gnfd-cmd -c config.toml group head-member  --groupOwner ${AccountA} --headMember ${AccountB} gnfd://fungroup
the user is a member of the group
$ gnfd-cmd -c config.toml storage get gnfd://funbucket/story.txt ./story-copy.txt
download object story.txt successfully, the file path is ./story-copy.txt
```


