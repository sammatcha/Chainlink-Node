# hi c:
This is the [official documentation](https://docs.chain.link/docs/running-a-chainlink-node/) with the requirements

## Chainlink Node & PostgreSQL Requirements
Deployed Docker and Postgres on Linode <br>
Note: 
- SSH Acess of Docker Linode to VS Code
- Goerli


## Steps to deploying Chainlink Node!
### Using Docker
1. ssh into server as root
```
ssh root@ XXX.XXX.XXX.XX
```
```
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker $USER
exit
#log in again
```
Note: I used Debian

2. ssh back into your server
### Create a directory
Local directory to hold Chainlink data
``` 
mkdir ~/.chainlink-goerli
```

### Create an Environment File
```
echo "ROOT=/chainlink
LOG_LEVEL=debug
ETH_CHAIN_ID=5
CHAINLINK_TLS_PORT=0
SECURE_COOKIES=false
ALLOW_ORIGINS=*" > ~/.chainlink-goerli/.env
```
### Set Ethereum Client URL (External Provider)
I used Infura
```
echo "ETH_URL=CHANGEME" >> ~/.chainlink-goerli/.env
```
Note: ChangeMe is where the Infura url goes

## Set Database_URL Environment Variable
I created database, chainlink1 though PSQL <br>
Note:
to get into psql:
```
psql -h lin-9769-2282-pgsql-primary.servers.linodedb.net -U linpostgres -d postgres
```
- the server name is from db made in linode
once in psql:
``` 
CREATE DATABASE chainlink1;
```
3. ssh back into docker linode
### Set remote Database_URL Config
```
echo "DATABASE_URL=postgresql://$USERNAME:$PASSWORD@$SERVER:$PORT/$DATABASE" >> ~/.chainlink-goerli/.env
```
##### Replace $USERNAME, $PASSWORD, $SERVER, $PORT, $DATABASE respectively
##### information is found on linode db
Note: I had to cat .env to look at my environment file to ensure I had everything needed
steps:
```
1. ssh linode
2. ls -la
3. cd goerli
4. ls -la
5. cat .env
```

# Start Chainlink Node
4. Log into instance:
```
ssh <user ip> -L 6688:localhost:6688
```
Run docker:
```
cd ~/.chainlink-goerli && docker run -p 6688:6688 -v ~/.chainlink-goerli:/chainlink -it --env-file=.env smartcontract/chainlink:<version> local n
```
Note: version is the desired version on docker hub
- I am using 1.8.1-root

Problems encountered:
- I already had a database called chainlink, so I had to make a new one, chainlink1
to revise because I set my database as chainlink:
```
nano .env
```
revised, ctrl x, y, enter
- I ran the run command a few times so I had to 
```
docker stop container name
docker ps -a
docker container prune
```
and docker run again<br>

You will be prompted to create a new API key, API email & Password<br>
http://localhost:6688 into browser <br>
and wao , I'm in!<br>
<img width="358" alt="image" src="https://user-images.githubusercontent.com/60681017/193965000-99aa2c07-9a9e-4e26-80f2-b8fc56fb1e0a.png"> <br>

You can ctrl P, ctrl Q to detach from container<br>
Note: I had to switch over to terminal because ctrl Q was a shortcut on VS Code
