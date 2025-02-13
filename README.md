# Local

Ref: https://github.com/opentensor/bittensor-subnet-template/blob/main/docs/running_on_staging.md

```sh
# source ~/w/genereos/bittensor-start/bt_venv/bin/activate
# source ~/w/genereos/bittensor-start/btsdk_venv/bin/activate

rm -rf ~/.bittensor/wallets
btcli wallet new_coldkey --wallet.name owner --no-use-password --n-words 12 & \
btcli wallet new_coldkey --wallet.name owner2 --no-use-password --n-words 12 & \
btcli wallet new_coldkey --wallet.name miner --no-use-password --n-words 12 & \
btcli wallet new_coldkey --wallet.name validator --no-use-password --n-words 12

btcli wallet new_hotkey --wallet.name miner --wallet.hotkey default --n-words 12 & \
btcli wallet new_hotkey --wallet.name validator --wallet.hotkey default --n-words 12


# btcli wallet regen-coldkey --wallet.name genpool-validator --no-use-password --mnemonic ""
# btcli wallet regen-hotkey --wallet.name genpool-validator --no-use-password --wallet.path "~/.bittensor/wallets" --mnemonic ""

btcli w list

# btcli wallet new_coldkey --wallet.name genpool-validator --no-use-password --n-words 12 --wallet.path "~/.bittensor/wallets"
# btcli wallet new_hotkey --wallet.name genpool-validator --wallet.hotkey default --n-words 12 --wallet.path  "~/.bittensor/wallets"


# rm -rf target
# BUILD_BINARY=1 ./scripts/localnet.sh False
cd subtensor
BUILD_BINARY=0 ./scripts/localnet.sh False

btcli wallet faucet -y -u 5000 --max-successes 5 --wallet.name owner & \
btcli wallet faucet -y -u 5000 --max-successes 5 --wallet.name owner2 & \
btcli wallet faucet -y -u 5000 --max-successes 5 --wallet.name miner & \
btcli wallet faucet -y -u 5000 --max-successes 5 --wallet.name validator

# btcli wallet faucet -y -u 5000 --max-successes 5 --wallet.name genpool-validator

# View Balance all
btcli w inspect -a

btcli subnet list
btcli subnet create --wallet.name owner -y
btcli subnet register --wallet.name miner     --wallet.hotkey default --netuid 1 -y
btcli subnet register --wallet.name validator --wallet.hotkey default --netuid 1 -y
btcli stake add --wallet.name validator --wallet.hotkey default --amount 1000 -y

#
# At this point we can start miner and validator
# It vary base on actual subnet document.
#

# btcli subnet create --wallet.name owner2 -y
# btcli subnet list
# btcli subnet register --wallet.name miner     --wallet.hotkey default --netuid 2 -y
# btcli subnet register --wallet.name validator --wallet.hotkey default --netuid 2 -y
# btcli stake add --wallet.name validator --wallet.hotkey default --amount 1000 -y

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

# Testnet

```sh
# Fix version of btcli
pip install bittensor-cli==8.2.0rc9

# btcli --version
# BTCLI version: 8.2.0rc9

btcli w list
# Using the wallet path from config: ~/.bittensor/wallets
# Wallets
# ├── Coldkey genpool-validator  ss58_address 5D7jns1Citgz8DDk1D8Mk32jjZE7yL5wB5EQVj9rNyFVN9aV
# │   └── Hotkey default  ss58_address 5HMawd8B3UDjLFkRgyUyjcHccjnwnxjrddHZZFpnuqa1YQGo


# check balance
btcli wallet balance --wallet-name genpool-validator --subtensor.chain_endpoint wss://test.finney.opentensor.ai:443

# Register
btcli subnet register --wallet.name genpool-validator --subtensor.chain_endpoint wss://test.finney.opentensor.ai:443 --wallet.hotkey default --netuid 209 -y

# Using the wallet path from config: ~/.bittensor/wallets
# Balance:
#   τ 10.1000 ➡ τ 10.1000
# ✅ Registered on netuid 209 with UID 9

# Staking
btcli stake add --wallet.name genpool-validator --subtensor.chain_endpoint wss://test.finney.opentensor.ai:443 --wallet.hotkey default --amount 1.5 --netuid 209 -y

# Using the wallet path from config: ~/.bittensor/wallets
#                                                                                                                            
#                                                         Staking to:                                                        
#                  Wallet: genpool-validator, Coldkey ss58: 5D7jns1Citgz8DDk1D8Mk32jjZE7yL5wB5EQVj9rNyFVN9aV                 
#                                                        Network: test                                                       
#                                                                                                                            
#  Netuid ┃                      Hotkey                      ┃ Amount (τ) ┃      Rate (per τ)      ┃   Received   ┃ Slippage 
# ━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━
#   209   │ 5HMawd8B3UDjLFkRgyUyjcHccjnwnxjrddHZZFpnuqa1YQGo │  τ 1.5000  │ 899.4504357837361 อ/τ  │ 1,328.4618 อ │ 1.5353 % 
# ────────┼──────────────────────────────────────────────────┼────────────┼────────────────────────┼──────────────┼──────────
#         │                                                  │            │                        │              │          
# 
# Description:
# The table displays information about the stake operation you are about to perform.
# The columns are as follows:
#     - Netuid: The netuid of the subnet you are staking to.
#     - Hotkey: The ss58 address of the hotkey you are staking to. 
#     - Amount: The TAO you are staking into this subnet onto this hotkey.
#     - Rate: The rate of exchange between your TAO and the subnet's stake.
#     - Received: The amount of stake you will receive on this subnet after slippage.
#     - Slippage: The slippage percentage of the stake operation. (0% if the subnet is not dynamic i.e. root).
# 
# ✅ Submitted τ 1.5000 to 209
```