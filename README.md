# COSMOS-IBC-HERMES-RELAYER


Install Hermes manually

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

cargo install ibc-relayer-cli --bin hermes --locked

hermes version

Add file ~/.hermes config.toml
[global]
log_level = "info"

[mode]
[mode.clients]
enabled = true
refresh = true
misbehaviour = true

[mode.connections]
enabled = true

[mode.channels]
enabled = true

[mode.packets]
enabled = true
clear_interval = 100
clear_on_start = true
tx_confirmation = true

[[chains]]
id = "natancoin-1"
rpc_addr = "http://127.0.0.1:26657"
grpc_addr = "http://127.0.0.1:9090"
rpc_timeout = "10s"
account_prefix = "natancoin"
key_name = "relayer"
store_prefix = "ibc"
max_gas = 3000000
gas_price = { price = 2000, denom = "unatancoin" }

  [chains.event_source]
  mode = "push"
  url = "ws://127.0.0.1:26657/websocket"
  batch_delay = "200ms"

[[chains]]
id = "cyntiacoin-1"
rpc_addr = "http://127.0.0.1:26659"
grpc_addr = "http://127.0.0.1:9092"
rpc_timeout = "10s"
account_prefix = "cyntiacoin"
key_name = "relayer"
store_prefix = "ibc"
max_gas = 3000000
gas_price = { price = 2000, denom = "ucyntiacoin" }

  [chains.event_source]
  mode = "push"
  url = "ws://127.0.0.1:26659/websocket"
  batch_delay = "200ms"


//END OF FILE

natancoind keys add relayer --keyring-backend test

- address: natancoin1gaec5pyxcwcvd3a4386yn2jntt4zz9pt7zaqtd
   name: relayer
   pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"Ah0s0pCs8sY+T+bjVMbsPpuRo0wqgAUFIGdqNhWz2jp1"}'
   type: local

        doctor sausage outer indoor check identify bus end antique mistake entire liar buddy humor cash abuse scan hen patient inquiry awful describe near survey


cyntiacoind keys add relayer --keyring-backend test

- address: cyntiacoin142k2njd49cgkscjz6a0xey4fv53vw8u5pyesgn
  name: relayer
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AozXs1ROLaYtD5Oqrnj9X393vyBO35iqWzuvtW445lgU"}'
  type: local


shaft sunset situate food history midnight guilt trigger imitate twelve radio minute hospital sauce define gentle egg rug able insane glue capital fatigue rule



# For natancoin
hermes keys add --chain natancoin-1 --key-name relayer --mnemonic-file /Users/natanaelmr/Desktop/BACKUP/natan/Go/cosmos/relayer/natancoin.txt

# For cyntiacoin
hermes keys add --chain cyntiacoin-1 --key-name relayer --mnemonic-file /Users/natanaelmr/Desktop/BACKUP/natan/Go/cosmos/relayer/cyntiacoin.txt


Send token to relayer

 natancoind tx bank send \
  natancoin14eztkklkglmgvqpu0uhycxmyzve99x66dpeqrv \
  natancoin1gaec5pyxcwcvd3a4386yn2jntt4zz9pt7zaqtd \
  10000000000unatancoin \
  --fees 400000000unatancoin \
  --keyring-backend test \
  --chain-id natancoin-1 \
  -y

 cyntiacoind tx bank send \
  cyntiacoin1lthd50p2cnsn7nptc42msjmhfs8geecvfm26e2 \
  cyntiacoin142k2njd49cgkscjz6a0xey4fv53vw8u5pyesgn \
  10000000000ucyntiacoin \
  --fees 400000000ucyntiacoin \
  --keyring-backend test \
  --chain-id cyntiacoin-1 \
  -y

Check balance 
natancoind query bank balances $(natancoind keys show alice -a --keyring-backend test)
natancoind query bank balances $(natancoind keys show relayer -a --keyring-backend test)
cyntiacoind query bank balances $(cyntiacoind keys show Alice -a --keyring-backend test)
cyntiacoind query bank balances $(cyntiacoind keys show relayer -a --keyring-backend test)


hermes query clients —host-chain natancoin-1
hermes query clients --host-chain cyntiacoin-1
hermes query connections —chain natancoin-1
hermes query connections --host-chain cyntiacoin-1

Hermes start

hermes create channel --a-chain natancoin-1 --b-chain cyntiacoin-1 \
  --a-port transfer --b-port transfer --new-client-connection

If error, ps aux | grep hermes then kill -9 <PID>
Then hermes start and create channel again

hermes create channel \
  --a-chain natancoin-1 \
  --a-connection connection-0 \
  --a-port transfer \
  --b-port transfer


Inspect channel on each chain

natancoind query ibc channel channels --output json
cyntiacoind query ibc channel channels --output json


Transfer dari natancoin ke cyntiacoin

 Prev balance
natancoin14eztkklkglmgvqpu0uhycxmyzve99x66dpeqrv : 1889600000000
cyntiacoin1lthd50p2cnsn7nptc42msjmhfs8geecvfm26e2 : 1890600000000


natancoind tx ibc-transfer transfer transfer channel-0 \
  cyntiacoin1lthd50p2cnsn7nptc42msjmhfs8geecvfm26e2 1000000000unatancoin \
  --from natancoin14eztkklkglmgvqpu0uhycxmyzve99x66dpeqrv \
  --chain-id natancoin-1 \
  --keyring-backend test \
  --gas auto --fees 4000000000unatancoin -y

 After balance
natancoin14eztkklkglmgvqpu0uhycxmyzve99x66dpeqrv : 1889600000000 -> 1884600000000 (-5000 natancoin)
cyntiacoin1lthd50p2cnsn7nptc42msjmhfs8geecvfm26e2 : 
balances:
- amount: "1000000000"
  denom: ibc/797EE030E206CBD3077E1E37B9D052C54624A9EF72C1188B998FB37202FCC67F
- amount: "1890600000000"
  denom: ucyntiacoin
pagination:
  total: "2"

cyntiacoind tx ibc-transfer transfer transfer channel-0 \
  natancoin14eztkklkglmgvqpu0uhycxmyzve99x66dpeqrv 1000000000ibc/797EE030E206CBD3077E1E37B9D052C54624A9EF72C1188B998FB37202FCC67F\
  --from cyntiacoin1lthd50p2cnsn7nptc42msjmhfs8geecvfm26e2 \
  --chain-id cyntiacoin-1 \
  --keyring-backend test \
  --gas auto --fees 4000000000ucyntiacoin -y


After balance
natancoin14eztkklkglmgvqpu0uhycxmyzve99x66dpeqrv : 1884600000000 -> 1885600000000 (+1000 natancoin)
cyntiacoin1lthd50p2cnsn7nptc42msjmhfs8geecvfm26e2 : 1886600000000 (unatancoin empty & - 4000 cyntiacoin for fee)
