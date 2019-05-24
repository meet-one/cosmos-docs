# Join the testnet

> tip: 
See the [testnets repo](https://github.com/cosmos/testnets) for
information on the testnet, including the correct version
of the Cosmos-SDK to use and details about the genesis file.

> warning:
**You need to [install gaia](./installation.md) before you go further**

## Setting Up a New Node

These instructions are for setting up a brand new full node from scratch.

First, initialize the node and create the necessary config files:

```bash
gaiad init <your_custom_moniker>
```

> warning Note
Monikers can contain only ASCII characters. Using Unicode characters will render your node unreachable.

You can edit this `moniker` later, in the `~/.gaiad/config/config.toml` file:

```toml
# A custom human readable name for this node
moniker = "<your_custom_moniker>"
```

You can edit the `~/.gaiad/config/gaiad.toml` file in order to enable the anti spam mechanism and reject incoming transactions with less than the minimum gas prices:

```
# This is a TOML config file.
# For more information, see https://github.com/toml-lang/toml

##### main base config options #####

# The minimum gas prices a validator is willing to accept for processing a
# transaction. A transaction's fees must meet the minimum of any denomination
# specified in this config (e.g. 0.025muon).

minimum-gas-prices = ""
```
> tip: On gaia-13003 testnet, the accepted denom is muon, where 1atom = 1.000.000muon

Your full node has been initialized! 

## Genesis

### Copy the Genesis File

Fetch the testnet's `genesis.json` file into `gaiad`'s config directory.

```bash
mkdir -p $HOME/.gaiad/config
curl https://raw.githubusercontent.com/cosmos/testnets/master/gaia-13k/genesis.json > $HOME/.gaiad/config/genesis.json
```

Note we use the `gaia-13k` directory in the [testnets repo](https://github.com/cosmos/testnets) which contains details for the testnet like the latest version and the genesis file.

## Run a Full Node

Start the full node with this command:

```bash
nohup gaiad start > gaiad.log 2>&1 &
```

Check that everything is running smoothly:

```bash
gaiacli status
```

View the status of the network with the [Cosmos Explorer](https://cosmos.network/launch).

# Run a Validator on the testnet

## Create Your Validator

Your `cosmosvalconspub` can be used to create a new validator by staking tokens. You can find your validator pubkey by running:

```bash
gaiad tendermint show-validator
```

To create your validator, just use the following command:

> tip:
You can get test cions from [Faucet](https://hubble.figment.network/cosmos/chains/gaia-13003/faucet)

> warning: 
Don't use more `muon` than you have!

```bash
gaiacli tx staking create-validator \
  --amount=1000000muon \
  --pubkey=$(gaiad tendermint show-validator) \
  --moniker="choose a moniker" \
  --chain-id=<chain_id> \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --gas="auto" \
  --gas-prices="0.025muon" \
  --from=<key_name>
```

> tip:
When specifying commission parameters, the `commission-max-change-rate` is used to measure % _point_ change over the `commission-rate`. E.g. 1% to 2% is a 100% rate increase, but only 1 percentage point.

> tip:
`Min-self-delegation` is a stritly positive integer that represents the minimum amount of self-delegated voting power your validator must always have. A `min-self-delegation` of 1 means your validator will never have a self-delegation lower than `1atom`, or `1000000muon`

You can confirm that you are in the validator set by using a third party explorer.