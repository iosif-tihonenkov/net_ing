# Настройка EIGRP
## Топология

Привет друг! 

Давай наачнем с тополоии сети. 
Для настройки взял C3745 - 3 штуки. На них и будем строить) Смотри скриншот ниже
<img width="639" height="297" alt="image" src="https://github.com/user-attachments/assets/47db3e1c-55dd-4779-9df0-52d55ee40050" />

## Перейдем к настройке

**RouterA, базовая настройка**

```
RouterA(config)# interface FastEthernet0/0
RouterA(config-if)# ip address 192.168.1.1 255.255.255.0  ! Назначаем IP-адрес и маску подсети для интерфейса Fa0/0
RouterA(config-if)# no shutdown                         ! Включаем интерфейс

RouterA(config)# interface FastEthernet0/1
RouterA(config-if)# ip address 192.168.3.1 255.255.255.0  ! Назначаем IP-адрес и маску подсети для интерфейса Fa0/1
RouterA(config-if)# no shutdown                         ! Включаем интерфейс
```


**RouterB, базовая настройка**
```
RouterB(config)# interface FastEthernet0/0
RouterB(config-if)# ip address 192.168.1.2 255.255.255.0  ! Назначаем IP-адрес и маску подсети для интерфейса Fa0/0
RouterB(config-if)# no shutdown                         ! Включаем интерфейс

RouterB(config)# interface FastEthernet0/1
RouterB(config-if)# ip address 192.168.2.1 255.255.255.0  ! Назначаем IP-адрес и маску подсети для интерфейса Fa0/1
RouterB(config-if)# no shutdown                         ! Включаем интерфейс
```

**RouterC, базовая нстройка**
```
RouterC(config)# interface FastEthernet0/0
RouterC(config-if)# ip address 192.168.2.2 255.255.255.0  ! Назначаем IP-адрес и маску подсети для интерфейса Fa0/0
RouterC(config-if)# no shutdown                         ! Включаем интерфейс

RouterC(config)# interface FastEthernet0/1
RouterC(config-if)# ip address 192.168.3.2 255.255.255.0  ! Назначаем IP-адрес и маску подсети для интерфейса Fa0/1
RouterC(config-if)# no shutdown                         ! Включаем интерфейс
```

**RouterA, настройка EIGRP:**
```
RouterA(config)# router eigrp 100                        ! Запускаем процесс EIGRP с номером автономной системы 100
RouterA(config-router)# network 192.168.1.0 0.0.0.255     ! Объявляем сеть 192.168.1.0/24 в EIGRP
RouterA(config-router)# network 192.168.3.0 0.0.0.255     ! Объявляем сеть 192.168.3.0/24 в EIGRP
```
**RouterB, настройка EIGRP:**
```
RouterB(config)# router eigrp 100                        ! Запускаем процесс EIGRP с номером автономной системы 100
RouterB(config-router)# network 192.168.1.0 0.0.0.255     ! Объявляем сеть 192.168.1.0/24 в EIGRP
RouterB(config-router)# network 192.168.2.0 0.0.0.255     ! Объявляем сеть 192.168.2.0/24 в EIGRP
```

**RouterC, настройка EIGRP:**
```
RouterB(config)# router eigrp 100                        ! Запускаем процесс EIGRP с номером автономной системы 100
RouterB(config-router)# network 192.168.1.0 0.0.0.255     ! Объявляем сеть 192.168.1.0/24 в EIGRP
RouterB(config-router)# network 192.168.2.0 0.0.0.255     ! Объявляем сеть 192.168.2.0/24 в EIGRP
```

## Проверка

На любом маршрутизаторе проверяем: 
```
show ip eigrp neighbors
show ip  route eigrp
```
Первое - это проверка соседей. Отображаются все пподключенные маршруты. Смотри на скрин ниже
<img width="740" height="396" alt="image" src="https://github.com/user-attachments/assets/ce2a22d3-ef2b-4071-99e9-f92b0b22006b" />

А если мы говорим с тобой про вторую команду - то мы проверяем маршрутизацию EIGRP 
Выглядит это так:
<img width="799" height="566" alt="image" src="https://github.com/user-attachments/assets/0a72707f-b94c-4273-9ca2-e2bef59bc6da" />

Ну и напоследок можно пропинговать все маршруты, если хочется. 

P.S. Важный момент - на всех маршртизаторах в области EIGRP должен быть одинаковый номер AS (автономной системы).
