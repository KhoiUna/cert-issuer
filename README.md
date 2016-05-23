# Digital Certificates Project

### Setting Up The VM (optional)
1. Install [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
2. Make a Linux VM (768MB of memory)
	* Make virtual harddisk (8GB) of VDI (Virtual Disk Image)
	* Select dynamically allocated memory
	* Download the .dmg file for Ubuntu [here](http://www.ubuntu.com/download/desktop)
	* Install Ubuntu on the VM by clicking the .dmg file and clicking through the install steps
7. Install Github `sudo apt-get install git`
8. Install pip `sudo apt-get install python-pip`
9. Install python-dev `sudo apt-get install python-dev`
9. Install virtualenv `sudo pip install virtualenv`

## Installation using APIs
Below are steps that will allow you to setup the digital certificates code to interact with the blockchain via the Blockchain.info API. If you do not have bitcoind already running on your computer, these instructions are highly reccomended.

1. Make a [blockchain.info](http://blockchain.info) wallet and designate an address that will store your bitcoin for issuing the certificates. This is not the same as your issuing address, which you should NOT add to your wallet. Transfer a few BTC to this address. FYI: This will be referenced as your `STORAGE_ADDRESS` in the code.
2. Verify your blockchain.info account via the link in your email.
3. Enable the blockchain.info API to access your wallet [Account Settings > IP Restrictions > Enable API Access]
4. Install the Blockchain.info API local service [instructions here](https://github.com/blockchain/service-my-wallet-v3). This will require you to request an API key, which may take 24 hours to recieve. This API key will be referenced as your `API_KEY` in the code. Note: You must check the "Create Wallets" checkbox under "Permissions" when requesting an API key in order for it to be compatible with the local service.

## Local Bitcoind Installation
Below are instructions are for running the code using bitcoind. To install bitcoind on a Ubuntu server, please follow the [tutorial here](https://21.co/learn/setup-a-bitcoin-development-environment/#installing-bitcoind-from-source-on-ubuntu).

1. Once your bitcoind instance is up and running, add in the Bitcoin address that you will use for issuing as a "watch address" using the command `bitcoin-cli importaddress "<insert_address_here>" ( "ISSUING_ADDRESS" rescan )`. This will take a while to run, since it will scan the blockchain for the address's previous transactions.


## Install 
1. Clone the repo: `git clone https://github.com/ml-learning/digital-certificates-v2.git`
2. Create a Python 3 virtual environment: `cd digital-certificates-v2 && virtualenv venv -p python3.4`
3. Activate the virtual environment and install the requirements: `source venv/bin/activate && venv/bin/pip install -r requirements.txt`
4. Create your conf.ini file from the template: `cp conf_template.ini conf.ini`

Next we will populate the values


## Creating Bitcoin Addresses

1. Go to [bitaddress.org](http://bitaddress.org)
2. Create an address that will be used as your 'issuing address', i.e. the address from which your certificates are issued.

     a. save the unencrypted private key to your USB drive, in a file called pk_issuing.txt
     b. save the public address as the issuing_address value in conf.ini

3. Create an address that will be used as your 'revocation address', i.e. the address you will spend from if you decide to revoke the certificates.

     a. save the unencrypted private key to your USB drive, in a file called pk_revocation.txt
     b. save the public address as the revocation_address value in conf.ini

4. Optional (TODO): create an address as above for the signing address
5. Optional (TODO): create an address as above for the storage address


Important! Do not plug in this USB when your computer's wifi is on.


## Create a bitcoin wallet and import your issuing address (instruction for blockchain.info -- adapt these for your environment)
This is one way to create a blockchain.info wallet that can perform transactions with your issuing address created above.
This assumes you have an blockchain.info API key with create wallet permissions.

TODO: these instructions aren't ideal. I eventually did this because I was having problems importing addresses. I'm sure
there is a better way.

1. Ensure your blockchain wallet service is started: `blockchain-wallet-service start --port 3000`
2. Create a wallet as follows.
  a. Create a data.json file with the following contents:
```
{
        "password": "create a new, secure password",                    <-- store this securely
        "api_code": "your_blockchain_info_api_code",
        "priv": "<the value in pk_issuing.txt>",
        "email": "<your_email>"
}

```
  b. Create the wallet with the above file:
`curl -X POST -H 'content-type: application/json' -d @data.json "http://localhost:3000/api/v2/create"`

  c. Note the response:

  ```
  {
    "guid":"<save this value as wallet_guid in conf.ini>",
    "address":"<note this matches your issuing address>"
  }

  ```

  c. Ensure you delete the data.json file and/or ensure it is moved to a secure location


3. Ensure the remaining required conf.ini values are entered. At this point you should have:

```
issuing_address = "<issuing address>"
revocation_address = "<revocation address>"

usb_name = "<path to usb>"
key_file = "<name of private key file>"

# The below fields are not needed for the local bitcoind installation, but are needed for the blockchain.info configuration
wallet_guid = "<blockchain.info wallet guid>"                    # Your unique identifier to login to blockchain.info
wallet_password = "<blockchain.info wallet password>"            # Your password to login to blockchain.info
storage_address = "<blockchain.info address with storage BTC>"
api_key = "<blockchain.info api key>"
```

### To Run
1. If you are using the blockchain.info API, start the blockchain.info server `blockchain-wallet-service start --port 3000`. Otherwise, ensure that bitcoind is running.
2. Add your certificates to data/unsigned_certs/
3. Make sure you have enough BTC in your storage address. (TODO: my blockchain calculations are lower!)
	1. Using bitcoind, each certificate costs 15000 satoshi ($0.06 USD)
	2. Using the blockchain.info API, each certificate costs: 26435 * total_num_certs + 7790 satoshi (e.g. if you are issuing 1 certificate, it will cost roughly $0.13 USD)
4. Run the create-certificates.py script to create your certificates: 
	1. To run "remotely" using the Blockchain.info API: `python create-certificates.py`
	2. To run using your bitcoind installation: `python create-certificates.py --remote=0`


## Contact
For questions or more information, contact Juliana Nazaré at [juliana@media.mit.edu](mailto:juliana@media.mit.edu).

## Kim details on wallet

1. Created issuing/revocation addresses as above

2. Created wallet associated with the above issuing address

