```sh
# mainnet
pm2 start --name net13-vali-updater --interpreter python scripts/start_validator.py -- --pm2_name net13-vali --wallet.name genpool-validator --wallet.hotkey default

# local testing
pm2 start --name net13-vali-updater --interpreter python scripts/start_validator.py -- --pm2_name net13-vali --wallet.name validator --wallet.hotkey default --netuid 1 --subtensor.chain_endpoint ws://127.0.0.1:9945

pm2 start --name net13-vali --interpreter python neurons/miner.py -- --wallet.name validator --wallet.hotkey default --netuid 1 --subtensor.chain_endpoint ws://127.0.0.1:9945 --subtensor.network local

# direct testing
python neurons/miner.py --wallet.name miner --wallet.hotkey default --netuid 1 --subtensor.chain_endpoint ws://127.0.0.1:9945 --subtensor.network local
python neurons/validator.py --wallet.name validator --wallet.hotkey default --netuid 1 --subtensor.chain_endpoint ws://127.0.0.1:9945 --subtensor.network local

# View log
pm2 logs --raw --lines 200 net13-vali-updater
pm2 logs --raw --lines 200 net13-vali
```
