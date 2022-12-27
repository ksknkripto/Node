##  Minimum Sistem Gereksinimleri

**CPU:** 4 CPU

**RAM:** 8 GB

**SSD:** 200 GB

##  Validator adı belirleme
```
MONIKER="Validator Adınız"
```

##  Node Güncellemeleri
```
sudo apt güncellemesi
```
```
sudo apt install curl git jq lz4 build-essential -y
```

##  Kurulum
```
sudo rm -rf /usr/yerel/git
sudo curl -Ls https://go.dev/dl/go1.19.linux-amd64.tar.gz | sudo tar -C /usr/yerel -xz
tee -a $HOME/.profile > /dev/null << EOF
dışa aktarma PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
kaynak $HOME/.profile
```

##  Github Klonlama
```
cd $ANA SAYFA
rm -rf artışı
git klonu https://github.com/UptickNetwork/uptick.git
cd artışı
inşa etmek
mkdir -p $HOME/.uptickd/cosmovisor/genesis/bin
mv build/uptickd $HOME/.uptickd/cosmovisor/genesis/bin/
rm -rf yapı
```


##  Servis Oluşturma
```
curl -Ls https://github.com/cosmos/cosmos-sdk/releases/download/cosmovisor%2Fv1.3.0/cosmovisor-v1.3.0-linux-amd64.tar.gz | katran xz
chmod 755 evren yöneticisi
sudo mv evren yöneticisi /usr/bin/kozmovizör
sudo tee /etc/systemd/system/uptickd.service > /dev/null << EOF
[Birim]
Açıklama=uptick-testnet düğüm hizmeti
After=network-online.target
[Hizmet]
Kullanıcı=$KULLANICI
ExecStart=/usr/bin/cosmovisor çalıştırma başlangıcı
Yeniden başlat=hata durumunda
Yeniden Başlat Sn=10
LimitNOFILE=65535
Ortam="DAEMON_HOME=$HOME/.uptickd"
Ortam="DAEMON_NAME=uptickd"
Ortam="UNSAFE_SKIP_BACKUP=doğru"
[Düzenlemek]
WantedBy=çok kullanıcılı.hedef
EOF
sudo systemctl daemon-yeniden yükleme
sudo systemctl uptickd'yi etkinleştir
ln -s $HOME/.uptickd/cosmovisor/genesis $HOME/.uptickd/cosmovisor/current
sudo ln -s $HOME/.uptickd/cosmovisor/current/bin/uptickd /usr/local/bin/uptickd
```


##  Node Yapılandırma
```
uptickd yapılandırma zinciri kimliği uptick_7000-2
uptickd yapılandırma anahtarlığı arka uç testi
uptickd yapılandırma düğümü tcp://localhost:15657
uptickd init $MONIKER --chain-id uptick_7000-2
curl -Ls https://snapshots.kjnodes.com/uptick-testnet/genesis.json > $HOME/.uptickd/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/uptick-testnet/addrbook.json > $HOME/.uptickd/config/addrbook.json
sed -i -e "s|^tohumlar *=.*|tohumlar = \"3f472746f46493309650e5a033076689996c8881@uptick-testnet.rpc.kjnodes.com:15659\"|" $HOME/.uptickd/config/config.toml
sed -i -e "s|^minimum gaz fiyatları *=.*|minimum gaz fiyatları = \"0auptick\"|" $HOME/.uptickd/config/app.toml
sed -i -e "s|^budama *=.*|budama = \"özel\"|" $HOME/.uptickd/config/app.toml
sed -i -e "s|^pruning-keep-recent *=.*|pruning-keep-recent = \"100\"|" $HOME/.uptickd/config/app.toml
sed -i -e "s|^pruning-keep-every *=.*|pruning-keep-every = \"0\"|" $HOME/.uptickd/config/app.toml
sed -i -e "s|^budama aralığı *=.*|budama aralığı = \"19\"|" $HOME/.uptickd/config/app.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:15658\"%; s%^laddr = \ "tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:15657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:15060 \"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:15656\"%; s%^prometheus_listen_addr = \":26660\ "%prometheus_listen_addr = \":15660\"%" $HOME/.uptickd/config/config.toml
sed -i.bak -e "s%^adres = \"tcp://0.0.0.0:1317\"%adres = \"tcp://0.0.0.0:15317\"%; s%^adres = \ ":8080\"%adres = \":15080\"%; s%^adres = \"0.0.0.0:9090\"%adres = \"0.0.0.0:15090\"%; s%^adres = \ "0.0.0.0:9091\"%adres = \"0.0.0.0:15091\"%; s%^adres = \"0.0.0.0:8545\"%adres = \"0.0.0.0:15545\"%; s%^ws-adresi = \"0.0.0.0:8546\"%ws-adresi = \"0.0.0.0:15546\"%" $HOME/.uptickd/config/app.toml
```

##  Node Başlatma
```
sudo systemctl start uptickd && journalctl -u uptickd -f --no-hostname -o cat
```

####  Senkronizasyon Hızlandırma

```
sudo systemctl uptickd'yi durdur
```

```
cp $HOME/.uptickd/data/priv_validator_state.json $HOME/.uptickd/priv_validator_state.json.backup
```

```
uptickd tendmint unsafe-reset-all --home $HOME/.uptickd
```


```
STATE_SYNC_RPC=https://uptick-testnet.rpc.kjnodes.com:443
STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@uptick-testnet.rpc.kjnodes.com:15656
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 2000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)
sed -i.bak -e "s|^etkinleştir *=.*|etkinleştir = doğru|" $HOME/.uptickd/config/config.toml
sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  $HOME/.uptickd/config/config.toml
sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  $HOME/.uptickd/config/config.toml
sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  $HOME/.uptickd/config/config.toml
sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.uptickd/config/config.toml
mv $HOME/.uptickd/priv_validator_state.json.backup $HOME/.uptickd/data/priv_validator_state.json
```

```
sudo systemctl start uptickd && journalctl -u uptickd -f --no-hostname -o cat
```




##  Node Kontrolü
```
uptickd durumu 2>&1 | jq .SyncInfo
```



##  Cüzdan Oluşturma
```
uptickd tuşları cüzdan ekler
```

##  Validator Oluşturma

```
uptickd tx staking oluşturma-doğrulayıcı \
--miktar=1000000artış \
--pubkey=$(uptickd tendmint show-doğrulayıcı) \
--takma ad=<takma ad> \
--chain-id=uptick_7000-2 \
--komisyon oranı=0,05 \
--commission-max-rate=0,20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1 \
--from=<cüzdan> \
--gaz ayarı=1.4 \
--gaz=otomatik \
-y
```
