# ki-deployer
A tutorial and a script to deploy Ki relays and validators (Devnet)


## Registering the validator
In order to be able to start validating blocks on the devnet, your wallet needs to be registered as a validator delegate. This can be done inside the wallet application (desktop and mobile). The following screenshots shows the validator registration process inside the wallet. First, from your wallet hit the option list button on the top right corner. Then, select the register delegate option. Finally chose a name for your delegate and hit create. Registering a delegate implies a fee of 25 DKI.

![Synchronizing relay](/img/del1.jpg) ![Synchronizing relay](/img/del2.jpg) ![Synchronizing relay](/img/del3.jpg)


## One command installation
In progress ...

## Manual installation
This is a break down of the installation steps. It allows a better understanding of what is happening in the background when using the one command installation script.

### Database
We first need to create the database to store the blockchain data. After this step, you must end up with a database ("kinet_db") and a user with a full access to it. Do it your way or follow these steps:

Install postgres
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```
Two, create the database
```bash
sudo -u postgres createdb kinet_db
```
Finally, create the user, and grant them access to the database
```bash
sudo -u postgres createuser <your_username_of_choice>
sudo -u postgres psql
ALTER USER <your_username> WITH ENCRYPTED PASSWORD '<your_password_of_choice>';
GRANT ALL PRIVILEGES ON DATABASE kinet_db TO <your_username>;
```

### KiChain core
#### Clone the repository
First, let's get the code of the core. The latest modifications will be pushed to the devnet/dev branch during the development phase.

```bash
git clone --single-branch --branch devnet/dev https://github.com/KiFoundation/core
```

#### Set the environment
And make sure you have installed an updated version of NodeJS & yarn:

```bash
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install build-essential
```
and then

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt-get update && sudo apt-get install yarn
```

Second, we need to setup the the environment's modules and dependencies. Let _lerna_ do the job ...
```bash
yarn global add lerna
cd core
lerna bootstrap
```
if the install failed because of rights issues, please try:

```bash
sudo npm install --g lerna
```

#### Configure the core
Finally, let's configure the core. This is done inside the _.env_ file. Start by navigating to your home directory and by creating the .ark/ folder and the _.env_  file inside it.

```bash
mkdir ~/.ark/ && cd ~/.ark/ && vim .env
```

fill the newly created file with the following configuration :
```
# The host to connect to the other nodes
ARK_P2P_HOST=0.0.0.0
ARK_P2P_PORT=4000

# The host to receive the API calls
ARK_API_HOST=0.0.0.0
ARK_API_PORT=4003

# Database configurations
ARK_DB_USERNAME=<your_username>
ARK_DB_PASSWORD=<your_password>
ARK_DB_DATABASE=kinet_db

# Increased rate limit (if you plan to connect your own explorer to the node)
ARK_API_RATE_LIMIT_USER_LIMIT=900
```


### Launch components
Now that the environment is set, you can launch your validator... But first let's configure the identity of the delegate. This is done by navigating to the delegate.json file and by filling in the secrete passphrase of the delegate:

Go to the delegates file:
```bash
cd ~/core/packages/core/lib/config/kinet/
vim delegates.json
```

It looks something like this :
```bash
{
  "secrets": ["word1 word2 word3 word4 etc..."]
}
```

Add your secret phrase between the square brackets.

#### Relay
Start by launching the relay:
```bash
cd ~/core/packages/core
yarn relay:kinet
```
Your relay will start synchronising the blockchain with the other peers on the network. This looks like this:

![Synchronizing relay](https://github.com/KiFoundation/ki-deployer/blob/master/img/relay-synch.png)

#### Validator
And then your validator...
```bash
yarn validator:kinet
```


## Credits
* __Genki Team:__
- [Reda Berrehili](https://github.com/berrehili)
- [Tarek Awwad](https://github.com/TarekAwwad)

* __Ark Original Team__
- [François-Xavier Thoorens](https://github.com/fix)
- [Kristjan Košič](https://github.com/kristjank)
- [Brian Faust](https://github.com/faustbrian)
- [Alex Barnsley](https://github.com/alexbarnsley)
- [All Contributors](../../contributors)

## License

[MIT](LICENSE) © [Genki](https://gen.ki) © [ArkEcosystem](https://ark.io)
