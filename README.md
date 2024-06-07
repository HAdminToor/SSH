# SSH

**7.	Настройте подключение по SSH для удалённого конфигурирования устройства HQ-SRV по порту 2222. Учтите, что вам необходимо перенаправить трафик на этот порт посредством контролирования трафика.**
## **HQ-SRV**

```
nano /etc/openssh/sshd_config
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/d2036a2d-11ac-4eb3-a7ae-17b6633ac0ae)  
```
ctrl-x
y
enter
systemctl restart sshd
```
## **HQ-R**
```
nft add table inet nat
nft add chain inet nat prerouting '{ type nat hook prerouting priority 0; }'
nft add rule inet nat prerouting ip daddr 192.168.0.2 tcp dport 22 dnat to 10.0.0.2:2222
nft list ruleset | tail -n 7 | tee -a /etc/nftables/nftables.nft
systemctl enable --now nftables
systemctl restart nftables
nft list ruleset
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/4bec0562-f600-4c8f-9a14-c67fa686edc4)  

Выполняем проверку подключения:  
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/d63221e1-a13a-44aa-8add-908d7bcd3f47)  

**8.	Настройте контроль доступа до HQ-SRV по SSH со всех устройств, кроме CLI.**

## **HQ-SRV**

```
systemctl enable --now nftables.service
nft add rule inet filter input ip saddr 10.0.1.2 tcp dport 2222 counter drop
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/6eeaf5e0-22d3-4342-89ec-ec5fd4a7fcbd)  

```
nano /etc/nftables/nftables.nft
```
Удаляем всё содержимое файла до закоментированных строк:  
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/2921301e-e2d5-464f-ad03-fc2eb33b9043)  

```
nft list ruleset | tee -a /etc/nftables/nftables.nft
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/718a8fb8-eb1a-4874-8d7c-76a39fbe9cc2)  

```
systemctl restart nftables
nft list ruleset
```
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/e1373d1d-bf3d-40fb-8aa0-2c8625ea1090)  

Выполняем проверку:  
## **CLI**
![image](https://github.com/NyashMan/DEMO2024/assets/1348639/735c0cdd-a2f7-4186-b2cd-91e5310724f1)  
В результате настройки, соединение с сервером **HQ-SRV** по ssh установить не удастся.  
