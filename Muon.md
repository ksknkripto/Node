# Muon network

## Ödül:

* Muon'nun kanarya chaininde (kusama gibi) node kurmak belirli sayıda token gerektiriyor (tüm PoS zincirlerinde ki gibi) bu katılanlara ücretsiz verilecek.
* [Sohbet Kanalı](https://t.me/MuonNetworkTurkish)
* [Muon DC](https://discord.gg/muon)

## Sistem gereksinimleri:
```
2 CPU
4 RAM
20 SSD
```

## İlk kurulum:
```
git clone https://github.com/muon-protocol/muon-node-js.git --recurse-submodules --branch testnet
```

* Docker compose kurulumu için;

```
apt install docker-compose
```

* Aşağıdaki kodu girdikten sonra kırmızı yazılar görmemiz gerek. Eğer kırmızı yazılar çıkarsa işlemlere devam edebilirsiniz.

```
cd muon-node-js
docker-compose build
```

## Node'u başlatmak için:

```
docker-compose up -d
```

![image](https://user-images.githubusercontent.com/101149671/213519322-d3ab9641-2eeb-4e19-bdd7-ea580ad089f6.png)

* Node çalışıyorsa üstteki görseldeki gibi DONE yazacaktır.
* Ardından "`http://serveripadresiniz:8000/status`" tırnak içindeki adresi kopyalayıp serveripadresiniz kısmına sunucu IP adresinizi yazıp gidin. 
* Sayfa açılırsa ve false yazıyorsa node çalışıyordur.

## Şimdi stake işlemi:

* [BSC Faucet](https://testnet.bnbchain.org/faucet-smart)'ten token alalım.
* [Buraya](https://alice.muon.net/join) girip cüzdanı bağlıyoruz.
* Ardından 1000 ALICE mintliyoruz. 
* Node'u başlattıktan sonra doğrulamak için girdiğimiz siteye girip
* `address` ve `peerId`'mizi alıyoruz. 
* Mint yaptığımız sitede Next Step deyip, approve yapıyoruz. Sonraki adımda address ve peerId giriyoruz. Metamask gas uyarısı verirse miktar kadar cüzdanda olduğundan emin olun "yine de devam et" deyin. 1000 ALİCE'yi stakeleyin. 
* Bir kaç dakika sonra buradan kontrol edin, `http://ipadresiniz:8000/v1/?app=tss&method=test`
* Bu sayfada `{"success":true,"result":{"confirmed":true, ... }}` yazıyorsa işlem tamam.
