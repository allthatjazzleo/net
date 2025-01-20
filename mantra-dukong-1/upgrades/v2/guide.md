# v1 to v2 Testnet Upgrade Guide

|                 |                                                          |
|-----------------|----------------------------------------------------------|
| Chain-id        | `mantra-dukong-1`                                        |
| Upgrade Version | `v2.0.0`                                                 |
| Upgrade Height  | 2628000                                                  |
| Countdown       | <https://www.mintscan.io/mantra-testnet/block/2628000>   |

## Memory Requirements

This upgrade will **not** be resource intensive. With that being said, we still recommend having 32GB of memory. If having 32GB of physical memory is not possible, the next best thing is to set up swap.

Short version swap setup instructions:

``` {.sh}
sudo swapoff -a
sudo fallocate -l 32G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

To persist swap after restart:

``` {.sh}
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

In depth swap setup instructions:
<https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-20-04>

## First Time Cosmovisor Setup

If you have never setup Cosmovisor before, follow the following instructions.

If you have already setup Cosmovisor, skip to the next section.

We highly recommend validators use cosmovisor to run their nodes. This
will make low-downtime upgrades smoother, as validators don't have to
manually upgrade binaries during the upgrade, and instead can
pre-install new binaries, and cosmovisor will automatically update them
based on on-chain SoftwareUpgrade proposals.

You should review the docs for cosmovisor located here:
<https://docs.cosmos.network/main/tooling/cosmovisor>

If you choose to use cosmovisor, please continue with these
instructions:

To install Cosmovisor:

``` {.sh}
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.6.0
```

After this, you must make the necessary folders for cosmosvisor in your
daemon home directory (\~/.mantrachain).

``` {.sh}
mkdir -p ~/.mantrachain
mkdir -p ~/.mantrachain/cosmovisor
mkdir -p ~/.mantrachain/cosmovisor/genesis
mkdir -p ~/.mantrachain/cosmovisor/genesis/bin
mkdir -p ~/.mantrachain/cosmovisor/upgrades
```

Copy the current v1 mantrachaind binary into the
cosmovisor/genesis folder and v1 folder.

```{.sh}
cp $GOPATH/bin/mantrachaind ~/.mantrachain/cosmovisor/genesis/bin
```

Cosmovisor is now ready to be set up for v1.

Set these environment variables:

```{.sh}
echo "# Setup Cosmovisor" >> ~/.profile
echo "export DAEMON_NAME=mantrachaind" >> ~/.profile
echo "export DAEMON_HOME=$HOME/.mantrachaind" >> ~/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.profile
echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.profile
source ~/.profile
```

## Cosmovisor Upgrade

Now, at the upgrade height, Cosmovisor will automatically download binary and upgrade to the v2 binary

<!-- ## Manual Option

1. Wait for Mantrachain to reach the upgrade height (2628000)

2. Look for a panic message, followed by endless peer logs. Stop the daemon

3. Run the following commands:

    ```{.sh}
    cd $HOME/mantrachain
    git pull
    git checkout v2.0.0
    make install
    ```

4. Start the mantrachain daemon again, watch the upgrade happen, and then continue to hit blocks -->

## Further Help

If you need more help, please:
    - go to <https://docs.mantrachain.io>
    - join our discord at <https://discord.gg/fHSqUng7Hy>.