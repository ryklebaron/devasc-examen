# Goal of this project

The goal of the project is to configure a cisco router with basic security tasks.
This can be done with the use of Ansible. I mainly use the **ios_config** module to achieve this.

> I created this playbook for my own course I am currnetly following **DevAsc** (Cisco)

# Cisco CSR1000v

Within this project i used the Cisco CSR1000 router which is a virtual router.
The router I used came from Cisco with the course and ssh was enabled by default.
Make sure you have ssh enabled on the router and are able to login with your credentials.

> The router I used had only one interface (GigabitEthernet1)

# What this playbook does
#### On the Router
1. Creates a backup of the running config of the routers 
2. Sets ipv4 addressess on interface GigabitEthernet1/2
3. Sets security settings 


# ssh-key

Now on your host which runs your ansible playbook, we need to:

1.  create ssh-key
2.  cut ssh-key in a few lines (explained when doing this)
3.  configure the router to use your ssh-key

## Generate ssh-key on your Linux machine

The following command will generate a ssh-key (public and private)

```
ssh-keygen
```

> I just press enter a few times to "OK" the default settings. I recommend this
> The keys (private and public) are stored in your home directory ~/.ssh/
> Here you can find your public and private keys
> NOTE!! NEVER share your private key

### Cut ssh-key in multiple lines

Because Cisco-IOS only supports up to 254 characters per line, we need to 
create multiple lines out of our id_rsa.pub key. We can do that by using the following command:

```
fold -b -w 72 /HOME/USERNAME/.ssh/id_rsa.pub
```

> replace "/HOME/USERNAME/ with the desired home/username/ you want to use for the ssh-key

Copy this output and login onto your router

## Use ssh-pubkey

1.  Go into config mode on your Cisco CRSV1000 router.
    
2.  execute the command:
    
    ```
    ip ssh pubkey-chain
      username cisco
      key-string
    ```
    

You are now in the **conf-ssh-pubkey-data** mode. Here you can past your previous copied id_rsa.pub key
. press enter and return to config mode.


## Check if ssh-key is working

check from your Linux machine if you can login with your ssh-key by executing the following command:

```
ssh username@ipAddressOrHostname # cisco@192.168.10.1
```

# Run Ansible

You can run ansible playbooks with a small wrapper script.

```
./run -l websers
```
> runs only the group **webservers** from inventory (hosts)

> Make sure the **run** file has execute permissions

This checks the site.yml file and looks for the **routers** group there.
I made it possible to just run a single playbook for a group. Use the **-t** for tag option:

```
./run -l routers -t rbase
```

This runs the playbook routers_base for the hosts within **routers**

# Run all playbooks against all hosts (entire site.yaml)

```
./run
```