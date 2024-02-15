---
description: xd
---

# Initial

nmap -A -T4 -sS -p- IP | tee nmapfull.txt\
\
nmap -A -T4 IP | tee nmap.txt

nmap IP -sU | nmapudp.txt&#x20;



FW EVASION

```
nmap --mtu 24 <IP>
nmap --data-length 30 <IP>
nmap --source-port 53 <IP>
```



WEB RECON



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



#### Website Mirroring

wget -m https://IP



wget -m -e robots=off https://IP





#### Pesquisa via requisições HTTP

nc -v IP 80

HEAD / HTTP/1.0

GET /index.php HTTP/1.0

OPTIONS /seila HTTP/1.0 (PARA ENTENDER OS METODOS QUE O SITE ACEITA)







