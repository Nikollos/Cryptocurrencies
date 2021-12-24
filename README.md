# Project 2 - Setup for Participants  

This exercise is intended as a group project. You should solve it as follows:
- Every participant of every group should have received a personal account and some personal contracts (see below for more info);
- Every participant of every group should try to experiment and exploit their own contracts;
- You are encouraged to try the challenges yourself and then work on a solution together WITHIN your group;
- You should write and submit one report per group, documenting your solutions, how you found them, what obstacles you encountered, etc.

You should have received the following via mail:

* A **public/private key pair** (i.e., an external account). This is the file starting with `UTC--`.
Your account should be initialized with a balance of `16` *EveCoins* (our private fake ether), which should be more than enough to solve all the challenges.

* The **password file** to your account. This is the file `passwordfile`.
It contains the password to unlock and use your account. 

* A `genesis.json` file describing the first block of our custom Proof-of-Authority (PoA) Ethereum blockchain. 

* The node information on how to find and connect to our custom Ethereum (geth) PoA chain.
This is the file `enodes`.

* The addresses of the personalized contracts to exploit in the challenges.
This is the file `addresses`.

## Task

- **Every member of every group:**
Your task is to exploit the (personalized) challenges and use their funds to increase the balance of **your** *external account*, 
which was initialized with `16` *EveCoins*. Of course you are free to work on the exploits together with your group members.
However, to *ease the grading* for us we expect that every member of the group exploits the challenges.
- **Once per group:**
In the end, prepare a short LaTeX report documenting your solution, the vulnerabilities, 
how you exploited them, perhaps also some commands you used, how they could be avoided, etc.
Please use the template we provide in TUWEL. We will mainly grade as indicated below, but 
keep in mind that a good report could give you some extra points while submitting a report 
with no explanations (e.g., only your commands) will decrease your points.
Not submitting a report will result in 0 points.
- **Challenges:**
You should have received the challenges via mail. Inside every challenge there is a `README.md` file that provides explanations.

All your challenges can award you with `100` *EveCoins* max. (`30*3 + 10`), including your initial funds (i.e., `16` *EveCoins*) you 
can get more than `100` *EveCoins*.

Grading is done by counting the *EveCoins* in your *external account* as follows:
1. More than 80 *EveCoins* on your external account 
    - and at least 4 personalized contracts exploited such that they have 0 funds 
2. More than 70 *EveCoins* on your external account
    - and at least 3 personalized contracts exploited such that they have 0 funds 
3. More than 60 *EveCoins* on your external account
    - and at least 2 personalized contracts exploited such that they have 0 funds
4. More than 50 *EveCoins* on your external account
    - and at least 2 personalized contracts exploited such that they have 0 funds

Be careful not to "burn" your *EveCoins* due to mistakes or in risky pyramid schemes. 
If your funds are gone they are gone! Hence, you might want to test your solutions first in a `ganache` test network as shown in the tutorial. 

[comment]: <> (In rare cases if you screwed up very badly and have a really good excuse we *might* bail you out, but then you have to write a _detailed_ report on how you solved all the challenges)

In case of questions, or confessing painful mistakes contact us on the TUWEL forum. 

**Note:** 
It is advisable to keep the commands you used for solving each challenge as a backup and as a reminder for you in case you have to "replay" your solutions on a newly initialized blockchain instance. Furthermore, you will need the commands when writing the report.

## Connect to our custom testnet

To connect to our private Ethereum PoA testnet you need our *genesis block*, which you should have received via mail. 
Do not modify this file. This file has to be identical, otherwise you will not be able to connect. 

- Ensure that the file `/genesis_config/go-ethereum/genesis.json` is the identical to the `genesis.json` file you have received from us via mail, otherwise replace the former with the latter.
- Copy your personal key file (the file starting with `UTC--`) into the folder `/datadir/bob/keystore` (and if the folder does not exist create it yourself).
- The `passwordfile` will later be used to unlock your account.
- Ensure that the enode information in `geth_run_bob.sh` is identical to the one you received per mail. Otherwise, replace the `enode` string in the following line with the value that you received from us:
```bash
export BOOTNODE="enode://7e2f25fbffc9210113452523b3d3823f6ee1f03752f0e9393d8bfb30dcf4ad3d60d73667ef6c25845171478a25ed8bd7e312d035a7cae0fe58e11c4f5a9738c5@ethereum.teaching.secpriv.wien:30303?discport=30303"
```
### Prerequisites

1. You might need to install `pwgen`:
```bash
$ apt-get install pwgen # or on macOS: brew install pwgen
```

2. Install docker ce

Install docker according to your respective distribution:
https://docs.docker.com/get-docker/

3. Build docker image

```bash
$ DOCKERFILE=smartenv.geth.stable.Dockerfile
$ VERSIONTAG=v1.9.23
$ bash docker_build_smartenv.sh
```

### Connect

Make sure that the system time of your computer is correct, this is relevant for our PoA setup. 
Then, initialize your client (Bob), and run it to connect to our server.
```bash
$ ntpdate ntp.ubuntu.com # or on macOS: sudo sntp -sS time.apple.com
$ bash geth_init_bob.sh
$ bash geth_run_bob.sh 
```

**Note:** When you connect to the server for the first time it can take a bit of time until you client (Bob) syncs with our testnet blockchain.

**Note:** Check if all your contracts have the right initial balances.
There should be 3 contracts with `30` *EveCoins* each and one with `10` *EveCoins*.
You can do this by running:
```bash
$ bash geth_attach.sh
```
If the command fails, you might need to set the container identifier of your docker container (which you can get by running `docker ps`), 
or change the port number to a port that is not in use. For example:
```bash
$ CONTAINERID=... RPCPORT=... bash geth_attach.sh
```

Once you connect to geth, then execute the following command, by replacing `"..."` with the addresses of your contracts found in the file `addresses` 
that you receive from us via mail:
```bash
eth.getBalance("...")
```

For more commands and some help refer to the Ethereum tutorial (and the recording of it on TUWEL), walk through it yourself or read the hints provided with some of the challenges.

## More hints

If you want, you can run a Jupyter notebook using:
```bash
$ bash docker_build_smartcode.sh
$ bash docker_run_smartcode.sh
```

Then, inside the notebook make sure to import and connect to your client (Bob) using the following:
```python
import sys
import requests
sys.path.append("/smartcode/util")
import util
from importlib import reload
reload(util)

# Connect to Bob

#w3=web3.Web3(web3.Web3.HTTPProvider("http://172.18.0.1:8545"))
# check if connection was successful
w3=util.connect(host="172.18.0.8",port="8545",poa=True)
assert w3.isConnected()
```

To unlock your account
```python
pw = "the password in passwordfile that you receive per mail"
address = w3.eth.accounts[0] # the address we want to unlock
w3.geth.personal.unlockAccount(address,pw,0) # third (optional) parameter is the duration (if 0 then forever)
```

### Create your own local ganache testnet

You should have more than enough coins to solve the challenges on our chain.
In case you want to be careful, you can (as mentioned in the tutorial) build your own testnet.
You can do this as follows and/or look at the tutorial. 
**Note:** Be sure, not to mix up the repository where you set up your own ganache with the one you use for solving the challenges on our server.

In order to build local testnet, if not already done, do the following:

```bash
$ DOCKERFILE=smartenv.geth.stable.Dockerfile
$ VERSIONTAG=v1.9.23
$ bash docker_build_smartenv.sh
```

Then, build ganache:

```bash
$ bash docker_build_ganache.sh
```

Follow the instructions in `README_TUTOR.md` up to "Test client bob". 
