# Local

Ref: https://github.com/opentensor/bittensor-subnet-template/blob/main/docs/running_on_staging.md

```sh
# source ~/w/genereos/bittensor/bt_venv/bin/activate

rm -rf ~/.bittensor/wallets
btcli wallet new_coldkey --wallet.name owner --no-use-password --n-words 12 --wallet.path "~/.bittensor/wallets" & \
btcli wallet new_coldkey --wallet.name miner --no-use-password --n-words 12 --wallet.path "~/.bittensor/wallets" & \
btcli wallet new_coldkey --wallet.name validator --no-use-password --n-words 12 --wallet.path "~/.bittensor/wallets"

btcli wallet new_hotkey --wallet.name miner --wallet.hotkey default --n-words 12 --wallet.path  "~/.bittensor/wallets" & \
btcli wallet new_hotkey --wallet.name validator --wallet.hotkey default --n-words 12 --wallet.path  "~/.bittensor/wallets"

# rm -rf target
# BUILD_BINARY=1 ./scripts/localnet.sh False
BUILD_BINARY=0 ./scripts/localnet.sh False

btcli wallet faucet -y -u 5000 --wallet.name owner & \
btcli wallet faucet -y -u 5000 --wallet.name miner & \
btcli wallet faucet -y -u 5000 --wallet.name validator

btcli wallet inspect -a

btcli subnet list
btcli subnet create --wallet.name owner -y
btcli subnet register --wallet.name miner     --wallet.hotkey default --netuid 1 -y
btcli subnet register --wallet.name validator --wallet.hotkey default --netuid 1 -y

btcli stake add --wallet.name validator --wallet.hotkey default --amount 1000 -y

# Review
btcli subnet list
btcli wallet overview --wallet.name miner
btcli wallet overview --wallet.name validator

# Start miner and validator in bittensor-subnet-template
python neurons/miner.py     --subtensor.chain_endpoint ws://127.0.0.1:9944 --netuid 1 --wallet.name miner     --wallet.hotkey default --logging.debug
python neurons/validator.py --subtensor.chain_endpoint ws://127.0.0.1:9944 --netuid 1 --wallet.name validator --wallet.hotkey default --logging.debug

# Start miner and validator in web-genie-ai
miner
python neurons/miners/miner.py --subtensor.chain_endpoint ws://127.0.0.1:9944 --netuid 1 --wallet.name miner --wallet.hotkey default --logging.debug

#  Set weights for your subnet
# Register a validator on the root subnet and boost to set weights for your subnet. This is a necessary step to ensure that the subnet is able to receive emmissions.
btcli root register --wallet.name validator --wallet.hotkey default -y
btcli root boost    --wallet.name validator --wallet.hotkey default --increase 1 --netuid 1 -y

btcli wallet overview --wallet.name miner

```

# Mainnet

```sh
btcli subnets list --subtensor.chain_endpoint wss://entrypoint-finney.opentensor.ai:443
```
