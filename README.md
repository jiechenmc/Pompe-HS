# Pompe-Hotstuff

This repo contains our implementation of Pompe on top of HotStuff. Please refer to our OSDI'20 paper and contact Yunhao Zhang(yz2327@cornell.edu) for questions about this repo.

## Table of Contents

* [Build and Run](https://github.com/Pompe-org/Pompe-HS/blob/master/README.md#build-and-run)
* Validate the Claims in Evaluation
* Troubleshooting

## Build and Run
### Build and run on local machine

My Linux username is `yunhao` and machine name is `driver`, so the `yunhao@driver:` below indicates my shell prompt. 
Replace the `yunhao` and `driver` below with your own username and machine name.


```shell
# Feel free to change the /home/yunhao below to any other directory
yunhao@driver: export POMPE_HOME=/home/yunhao/Pompe-HS
yunhao@driver: cd /home/yunhao/
yunhao@driver: git clone --recursive git@github.com:Pompe-org/Pompe-HS.git

# Install dependencies
yunhao@driver: cd $POMPE_HOME
yunhao@driver: ./install_deps.sh

# Build Pompe
yunhao@driver: cd $POMPE_HOME/libhotstuff
yunhao@driver: ./build.sh

# Run 4 Pompe servers
yunhao@driver: cd $POMPE_HOME/experiments/pompe
yunhao@driver: ./run_server.sh yunhao local

# Run 4 Pompe clients for a 30 second experiment
yunhao@driver: cd $POMPE_HOME/experiments/pompe
yunhao@driver: ./run_client.sh yunhao 4 30 local
# output will look like:
...
client write to order log file /home/yunhao/Pompe-HS/experiments/pompe//log/client0.order.log, 58342 entries
client write to exec log file /home/yunhao/Pompe-HS/experiments/pompe//log/client0.exec.log, 57233 entries
# which means that, in 30 seconds, the client has processed 
# the ordering phase of 58342 commands
# and the consensus phase of 57233 commands

# Kill the Pompe servers and clients
yunhao@driver: ./kill.sh yunhao local
```

### generate configuration files

The Python script `$POMPE_HOME/libhotstuff/scripts/gen_conf.py` generates the configuration files. First, modify the IP addresses of the server nodes according to your experiment environment.

```python
    # in hotstuff/scripts/gen_conf.py
    ...
        # datacenter small
        ips = ['10.0.0.4', '10.0.0.5', '10.0.0.6', '10.0.0.7']
        # modify this ips variable to your own server list
    ...
```

Then generate your configuration files and replace the old ones.

```shell
yunhao@driver: cd $POMPE_HOME/libhotstuff
yunhao@driver: python scripts/gen_conf.py

# the configuration files are generated in `./conf-gen` directory. 
yunhao@driver: cp ./conf-gen/* $POMPE_HOME/experiments/pompe/conf-distributed

# modify the client and server machine IPs
yunhao@driver: vim $POMPE_HOME/experiments/pompe/conf-distributed/client.hosts
yunhao@driver: vim $POMPE_HOME/experiments/pompe/conf-distributed/server.hosts
# put all client and server IPs into the file below for deployment
yunhao@driver: vim $POMPE_HOME/hosts
```

Now you are ready to run Pompe in your own distributed environment.

> :warning: **[WARNING]** Revision in progress, expected to finish revision soon.
