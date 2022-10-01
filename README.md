<h1 align="center"> Wormholes Chain </h1>

![image](https://user-images.githubusercontent.com/101149671/193398451-1924bbed-747f-4493-a148-b9ee0837028e.png)

# Sorularınız için: [Türkiye Telegram Grubu](https://t.me/WormholesChainTurkish)

## Önce Sunucumuzu güncelliyoruz:
```
sudo apt-get update && sudo apt-get upgrade
```

## şimdi Docker Kurulumu Yapıyoruz:
```
sudo apt install docker.io
sudo systemctl enable --now docker
systemctl restart docker.service
```

## Kurulum için servis dosyası oluşturuyoruz aşağıdaki komudu yazın 
```
nano ruesandora.sh 
```
## açılan ekranda aşağıdaki kodları tek seferde copy past yapın CTRL+X ve Y ile kaydedip çıkın
```
#!/bin/bash
#check docker cmd
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

docker stop wormholes > /dev/null 2>&1
docker rm wormholes > /dev/null 2>&1
docker rmi wormholestech/wormholes:v1 > /dev/null 2>&1

if [ -d /wm/.wormholes/keystore ]; then
   read -p "Whether to clear the Wormholes blockchain data history, if yes, press the “y” button, and if not, click “enter.”：" xyz
   if [ "$xyz" = 'y' ]; then
         rm -rf /wm/.wormholes
              read -p "Enter your private key：" ky
   else
         echo "Do not clear"
   fi
else
   read -p "Please import your private key：" ky
fi

mkdir -p /wm/.wormholes/wormholes
if [ -n "$ky" ]; then
   echo $ky > /wm/.wormholes/wormholes/nodekey
fi

docker run -id -e KEY=$ky  -p 30303:30303 -p 8545:8545 -v /wm/.wormholes:/wm/.wormholes --name wormholes wormholestech/wormholes:v1

echo "Your private key is:"
sleep 6
docker exec -it wormholes /usr/bin/cat /wm/.wormholes/wormholes/nodekey
```

## Şimdi Kurulum için servis dosyamızı çalıştırıyoruz
```
bash ./ruesandora.sh 
```

## Şimdi Wormholes wallet'tan private keyi alalım: [Wallet Linki](https://www.limino.com/#/wallet)

* Setings kısmına giriyoruz: 

![image](https://user-images.githubusercontent.com/101149671/193398695-a53afc57-c569-4af4-b370-98f3ee27c384.png)

* Security kısmına girelim:
![image](https://user-images.githubusercontent.com/101149671/193398742-413fb950-d0e5-4246-927a-e2e45421afb7.png)

* Şifre vs. girip private keyi alın:

![image](https://user-images.githubusercontent.com/101149671/193400080-9dae0666-a6cd-4530-aa60-aff8ad43f09f.png)

## Yukarıda ruesandora.sh komutunu girdikten sonra az önce aldığımız private keyi girip enterliyoruz:

* Enter yaptğınızda bu çıktı çıkacak, kaydedin bunları ve private keyinizi

![image](https://user-images.githubusercontent.com/101149671/193398861-76f7563c-98ff-448f-bb9c-43e5eefc2f77.png)

## bu komutla kontrol ediyoruz:
```
docker ps
```

![image](https://user-images.githubusercontent.com/101149671/193398899-5151d453-cfca-4d59-8754-7810dbfd2bf9.png)

## Şimdi wormholes cüzdanına geri dönüyoruz:

* Ayarlardan become a validator sekmesine girelim:
* Confirm ve 2 onay ile validator oluşturalım:
* En az 70001 tokenınız olması lazım

![image](https://user-images.githubusercontent.com/101149671/193398933-2aa343ca-dbb6-4015-87d8-e2a11fb9931e.png)


## Bu [explorerdan](https://www.wormholesscan.com/#/Validator) cüzdanınızı aratarak validatörü kontrol edebilirsiniz:

![image](https://user-images.githubusercontent.com/101149671/193398985-ce40edaa-a4b7-4dfb-9bd9-d9ab5ec5ea8d.png)








