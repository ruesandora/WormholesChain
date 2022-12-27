

## Versiyon kontrol ediyoruz güncel versiyon V0.11.1
```
curl -X POST -H "Content-Type:application/json" --data '{"jsonrpc":"2.0","method":"eth_version","id":64}' http://127.0.0.1:8545
```
## Çıktı
```
V0.11.0
```


## Kurulum için servis dosyası oluşturuyoruz aşağıdaki komudu yazın 
```
nano ruesandora.sh 
```
##Açılan ekranda CTRL+K ile tektek satırları silin aşağıdaki kodları tek seferde copy past yapın CTRL+X ve Y ile kaydedip çıkın

```
#!/bin/bash
#check docker cmd
echo "Script version Number: v0.11.1"
which docker >/dev/null 2>&1
if  [ $? -ne 0 ] ; then
        echo "docker not found, please install first!"
        echo "ubuntu:sudo apt install docker.io -y"
        echo "centos:yum install  -y docker-ce "
        echo "fedora:sudo dnf  install -y docker-ce"
        exit
fi
#check docker service
docker ps > /dev/null 2>&1
if [ $? -ne 0 ] ; then

        echo "docker service is not running! you can use command start it:"
        echo "sudo service docker start"
        exit
fi

vt5=1672137578
vl=$(wget https://docker.wormholes.com/version>/dev/null 2>&1 && cat version|awk '{print $1}')
vr=$(cat version|awk '{print $2}' && rm version)
worm=$(docker images|grep "wormholestech/wormholes"|grep "v1")
if [ -n "$worm" ];then
        ct=$(docker inspect wormholestech/wormholes:v1 -f {{.Created}})
        cts=$(date -d "$ct" +%s)
        if [ $cts -eq $vl ];then
                container=$(docker ps -a|awk '{if($NF == "wormholes")print $0}')
                if [[ $container =~ "Up" ]];then
                        while true
                        do
                                key=$(docker exec -it wormholes /usr/bin/ls -l /wm/.wormholes/wormholes/nodekey)
                                if [ -n "$key" ];then
                                        echo -e "It is the latest version: $vr \nYour private key:"
                                        docker exec -it wormholes /usr/bin/cat .wormholes/wormholes/nodekey
                                        echo -e "\n"
                                        exit 0
                                else
                                        sleep 5s
                                fi
                        done
                elif [[ $container =~ "Exited" ]];then
                        echo -e "Your peer isn't running\nYou can use 'docker start wormholes' to start the node"
                        exit 0
                else
                        docker rm wormholes
                        read -p "Enter your private key：" ky
                fi
        elif [ $cts -lt $vl ];then
                docker stop wormholes > /dev/null 2>&1
                docker rm wormholes > /dev/null 2>&1
                docker rmi wormholestech/wormholes:v1 > /dev/null 2>&1
                if [ $cts -lt $vt5 ];then
                        if [ -f /wm/.wormholes/wormholes/nodekey ];then
                                echo "Clearing historical data ............"
                                cp /wm/.wormholes/wormholes/nodekey /wm/nodekey
                                rm -rf /wm/.wormholes
                                mkdir -p /wm/.wormholes/wormholes
                                mv /wm/nodekey /wm/.wormholes/wormholes/
                        else
                                read -p "Enter your private key：" ky
                        fi
                elif [ $cts -ge $vt5 ];then
                        if [ ! -f /wm/.wormholes/wormholes/nodekey ];then
                                read -p "Enter your private key：" ky
                        fi
                fi
        fi
else
        read -p "Enter your private key：" ky
fi

if [ -n "$ky" ]; then
        mkdir -p /wm/.wormholes/wormholes
        if [ ${#ky} -eq 64 ];then
                echo $ky > /wm/.wormholes/wormholes/nodekey
        elif [ ${#ky} -eq 66 ] && ([ ${ky:0:2} == "0x" ] || [ ${ky:0:2} == "0X" ]);then
                echo ${ky:2:64} > /wm/.wormholes/wormholes/nodekey
        else
                echo "the nodekey format is not correct"
                exit 1
        fi
fi

docker run -id -p 30303:30303 -p 8545:8545 -v /wm/.wormholes:/wm/.wormholes --name wormholes wormholestech/wormholes:v1 >/dev/null 2>&1 &

while true
do
        s=$(docker ps -a|grep "Up"|awk '{if($NF == "wormholes") print $NF}'|wc -l)
        key=$(docker exec -it wormholes /usr/bin/ls -l /wm/.wormholes/wormholes/nodekey 2>/dev/null)
        if [[ $s -gt 0 ]] && [[ "$key" =~ "nodekey" ]];then
                echo "Your private key is:"
                docker exec -it wormholes /usr/bin/cat /wm/.wormholes/wormholes/nodekey
                echo -ne "\n"
                docker exec -it wormholes ./wormholes version|grep "Version"|grep -v go
                break
        else
                sleep 5s
        fi
done
```

## Şimdi Kurulum için servis dosyamızı çalıştırıyoruz /// V0.11.1
```
bash ./ruesandora.sh 
```

## Versiyon kontrol ediyoruz güncel versiyon V0.11.1
```
curl -X POST -H "Content-Type:application/json" --data '{"jsonrpc":"2.0","method":"eth_version","id":64}' http://127.0.0.1:8545
```
##Çıktı
```
V0.11.1
```

## Node bloklarını izlemek servis dosyası oluşturuyoruz aşağıdaki komudu yazın ( Daha önceki bunu kurduysanız bu adımıgeçin)
```
nano monitor.sh 
```
## açılan ekranda CTRL+K ile tektek satırları silin aşağıdaki kodları tek seferde copy past yapın CTRL+X ve Y ile kaydedip çıkın


```
#!/bin/bash
function info(){
     cn=0
     while true
     do
             echo "$cn second."
             echo "node $1"
             rs=`curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","id":64}' https://api.wormholestest.com 2>/dev/null`
             blockNumbers=$(parse_json $rs "result")
             echo "Block height of the whole network: $((16#${blockNumbers:2}))"
             rs1=`curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_peerCount","id":64}' 127.0.0.1:$1 2>/dev/null`
             count=$(parse_json $rs1 "result")
             echo "Number of node connections: $((16#${count:2}))"
             rs2=`curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","id":64}' 127.0.0.1:$1 2>/dev/null`
             blckNumber=$(parse_json $rs2 "result")
             echo "Block height of the current peer: $((16#${blckNumber:2}))"
             sleep 5
             clear
             let cn+=5
     done
}

function parse_json(){
      if [[ $# -gt 1 ]] && [[ $1 =~ $2 ]];then
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
bash ./monitor.sh
```



