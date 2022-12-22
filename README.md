# Cosmos-KMS

This roll will install a KMS server ready to be integrated with a Cosmos SDK Chain. It will not initalize the KSM with your private key. 


## Paramaters

`kms_user`: (default: kms) user the KMS will run as  
`kms`: (default: tmkms) Only tmkms is supported now  
`kms_mode`: (default: undefined) softkms or yubihsm  
`kms_home`: (defult: /home/`kms_user`) Path where kms will hold its file 
`account_key_prefix`: (default: cosmopub) account key prefix for the chain  
`consensus_key_prefix`: (default: cosmosvalconspub) consesnu  key prefix  for the chain  
`chain_id`: Chain id for chain you are deploying  
`kms_hsm_id`: yubihsm index of key to use. To initialize use `1`  
`kms_hsm_pass`: yubihsm password for key. To initialize use `password`  


## Import the gaia private key to softkms

```
cp /home/gaia/.gaia/config/priv_validator_key.json /home/kms
chown kms.kms /home/kms/priv_validator_key.json 
su -s /bin/bash  kms
cd ~
tmkms softsign import priv_validator_key.json ~/.tmkms/config/secrets/priv_validator_key
```

## Import gaia private key into yubikey

Make sure `/home/kms/.tmkms/config/tmkms.toml` auth line looks like this
`auth = { key = 1, password = "password" }`

```
cp /home/gaia/.gaia/config/priv_validator_key.json /home/kms
chown kms.kms /home/kms/priv_validator_key.json 
su -s /bin/bash  kms
tmkms yubihsm setup
tmkms yubihsm keys import -t json -i 1  ~/.gaia/config/priv_validator_key.json
```

Then modify the `/home/kms/.tmkms/config/tmkms.toml` auth line to be
```
auth = { key = 4, password = "kms-validator-password-xxxxxxxxxxxxxxxxxxxxx" }
```
