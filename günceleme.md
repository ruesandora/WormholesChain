# Wormholes güncellemesi  V.0.11.0 ( Ağ sıfırlandı yeniden başladı)

## Aşağıdaki komutları giriyoruz gerekli güncellemeyi indirip, açıyoruz:
```
wget https://github.com/wormholes-org/wormholes/archive/refs/tags/v0.11.0.tar.gz
```
```
tar -xvf v0.11.0.tar.gz
```

## İlgili klasörün içine giriyoruz:

```
cd wormholes-0.11.0
```

## Daha sonra nodu  başlatıyoruz:

* Ekrana çıkan soruda direk entera basıyoruz:

```
bash ./wormholes_install.sh 
```


## Nodun eşleştiğini görmek için aşağıdaki komudu giriyoruz:

```
nano ruesandora.sh
```
## Aşğıdaki komutları tek seferde copy past yapın  CTRL X ve Y yapıp entera basıp kaydedip çıkıyoruz 
```
#!/bin/bash
function info(){
     cn=0
     while true
     do
             echo "$cn second."
             echo "node $1"
             rs=`curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","id":64}' https://api.wormholes.com 2>/dev/null`
             blockNumbers=$(parse_json $rs "result")
             echo "Block height of the whole network: $((16#${blockNumbers:2}))"
             rs1=`curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_peerCount","id":64}' 127.0.0.1:$1 2>/dev/null`
             count=$(parse_json $rs1 "result")
             echo "Number of node connections: $((16#${count:2}))"
             rs2=`curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","id":64}' 127.0.0.1:$1 2>/dev/null`
             blckNumber=$(parse_json $rs2 "result")
             echo "Block height of the whole network: $((16#${blckNumber:2}))"
             sleep 5
             clear
             let cn+=5
     done
}
function parse_json(){
      if [[ $1 =~ $2 ]];then
         echo "${1//\"/}"|sed "s/.*$2:\([^,}]*\).*/\1/"
      else
         echo "0x0"
     fi
}
function main(){
     if [[ $# -eq 0 ]];then
             info 8545
     else
             info $1
     fi
}
main "$@"
```
## Güncel blokları görmek için servis dosyası çalıştırıyoruz aşağıdaki komudu yazın

```
bash ./ruesandora.sh
```

## Bir süre beklediniz Peer bulamadıysanız nodu restart yapın:

```
docker restart wormholes
```
