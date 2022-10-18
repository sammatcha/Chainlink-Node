## Run a Chainlink Node with me!
# Table of Contents
1. [Introduction](#introduction)
2. [Requirements](#requirements)
3. [Using Docker](#docker)
4. [Chainlink node Steps](#steps)


## Introduction <a name = "introduction"> </a>
Chainlink is a decentralized oracle network that enables smart contracts to be built on blockchain with real-world data. <br>

This is the [offical documentation]](https://docs.chain.link/docs/running-a-chainlink-node/) with the steps.<br>
- I will be going through the steps I went through to run a Chainlink node.<br>
- I used the Goerli options which is a test network.

## Requirements <a name = "requirements"> </a>
### LINK requirements:
- You can run a node with 0 LINK, but it will not be able to participate in requests
- There is an option to have test LINK sent to you, if you desire
### Chainlink Node requirements:
Minimum: at least 2 cores and 4 GB of RAM
### PostgreSQL Database requirements:
Minimum: at least 2 cores, 4 GB of RAM, 100 GB of storage
### Ethereum Client:
Connectivity to Eth client is required. You can run your own or use a third-party service

## Using Docker <a name = "docker"> </a>
I created a server using Linode with a Debian image. <br>
1. ssh into server as root
```
ssh root@ XXX.XXX.XXX.XX
```
2. Set up Docker using Debian
```
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker $USER
exit
# log in again
```
- this will log you out, so you will have to log in again.

3. ssh back into your server

## Steps

### Create a directory
4. Create local directory to hold Chainlink data:
```
mkdir ~/.chainlink-goerli
```
### Create an Environment File
4. Run the command to create environment file 
```
echo "ROOT=/chainlink
LOG_LEVEL=debug
ETH_CHAIN_ID=5
CHAINLINK_TLS_PORT=0
SECURE_COOKIES=false
ALLOW_ORIGINS=*" > ~/.chainlink-goerli/.env
```
- I used the given initial environment variables<br>
There is a full list of environment variables on the documentation. 

### Set your Eth Client URL
5. I used an external provider, Infura, to get my web3 websocket url
```
echo "ETH_URL=CHANGEME" >> ~/.chainlink-goerli/.env
```
`CHANGEME` is where the address goes

6. I created a PostgreSQL database on Linode
- this can take a little bit of time to be active

7. I created a database using PSQL<br>
- to get into psql:

```
psql -h <database host> -U <username> -d postgres
```

- database host and username is found in my database cluster on Linode<br>
Once in psql, create a database for your database environment
```
CREATE DATABASE <db name>;
```

8. ssh back into server

### Set Database_URL for environment file
```
echo "DATABASE_URL=postgresql://$USERNAME:$PASSWORD@$SERVER:$PORT/$DATABASE" >> ~/.chainlink-goerli/.env
```
Replace $VARIABLES with actual values:
- $USERNAME:database username (must be owner)
- $PASSWORD: user's password
- $SERVER: Server name or IP address of db server
- $PORT: port
- $DATABASE: db to use for chainlink node 
#### Note:
- all of these variables can be found on the db server
- $DATABASE is the one that was created above in psql

#### Tips:
Steps to ensure my environment file had everything I needed:

1. ssh to server
2. `ls -la`
- to list out all my files/ sub -directories check to see I have a directory called goerli
3. `cd goerli`
4. `ls -la`
- to see if I have a directory called .env
5. `cd .env`


### Start the Chainlink Node
9. Log into server:
```
ssh <user ip> -L 6688:localhost:6688
```
10. Run docker image
```
cd ~/.chainlink-goerli && docker run -p 6688:6688 -v ~/.chainlink-goerli:/chainlink -it --env-file=.env smartcontract/chainlink:<version> local n
```
- I am using version 1.8.1-root

### Conclusion
If it is your first time running the image, you will be prompted to create:
-a new API key, 
-API email & 
-password <br>

Connect to your Chainlink node's UI by putting localhost:6688 into your browswer <br>

and wao, I am in! Congrats! <br>
<img width="358" alt="image" src="https://user-images.githubusercontent.com/60681017/193965000-99aa2c07-9a9e-4e26-80f2-b8fc56fb1e0a.png"> <br>

### Note:
You can detach from container by pressing ctrl P, ctrl Q

## Problems I ran into:
1. I used a database in my first run for my second run.
- to revise my database name in my environment file:
``` nano.env
```
2. I ran the run command a few times before I found out what the problem was so I had to:
```
docker stop <container name>
docker ps -a
docker container prune
```
and I docker ran again
