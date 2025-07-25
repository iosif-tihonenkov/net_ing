# Настройка статического NAT
## Изначальная топология
<img width="848" height="451" alt="image" src="https://github.com/user-attachments/assets/546b2005-a403-4b9e-b5f8-85de71c5e340" />
У нас есть три маршрутизатора. Внутренний, пограничнй (NAT) и внешний.
Все в целом видно на картинке. Начнем? 

## Настройка R0
```
Router>enable ! Переход в привилегированный режим (enable mode)
Router#configure terminal ! Вход в режим глобальной конфигурации
Router(config)#hostname R0 ! Установка имени устройства на R0
R0(config)#interface FastEthernet0/0 ! Выбор интерфейса FastEthernet 0/0 для настройки
R0(config-if)#ip address 10.0.0.1 255.255.255.0 ! Назначение IP-адреса 10.0.0.1 с маской 255.255.255.0
R0(config-if)#no shutdown ! Включение интерфейса (активация порта)
R0(config-if)#exit ! Выход из режима настройки интерфейса
R0(config)#copy running-config startup-config ! Сохранение текущей конфигурации в память устройства
R0(config)#no ip routing ! Отключаем функцию маршрутизации на устройстве
R0(config)#ip default-gateway 10.0.0.2 ! Устанавливаем шлюз по умолчанию
```
## Настрока R1
```
Router>enable ! Переход в привилегированный режим (enable mode)
Router#configure terminal ! Вход в режим глобальной конфигурации
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#hostname R1 ! Установка имени устройства на R1
R1(config)#interface FastEthernet0/0 ! Выбор интерфейса FastEthernet 0/0 для настройки
R1(config-if)#ip address 10.0.0.2 255.255.255.0 ! Назначение IP-адреса 10.0.0.2 с маской 255.255.255.0
R1(config-if)#no shutdown ! Активация интерфейса (включение порта)
R1(config-if)#exit ! Выход из режима настройки интерфейса
R1(config)#interface FastEthernet0/1 ! Выбор второго интерфейса для настройки
R1(config-if)#ip address 212.100.100.2 255.255.255.0 ! Назначение внешнего IP-адреса
R1(config-if)#no shutdown ! Активация второго интерфейса
R1(config-if)#end ! Выход из режима конфигурации
R1(config)# ip nat inside source static 10.0.0.2 212.100.100.2 ! Настройка статической трансляции NAT
R1#copy running-config startup-config ! Сохранение конфигурации
```

Команда `ip nat inside source static 10.0.0.2 212.100.100.2 `выполняет статическую трансляцию NAT (Network Address Translation). И вот что она делает:

Статическая трансляция: Это означает, что преобразование IP-адресов выполняется вручную и не изменяется автоматически.
Inside source: Указывает, что внутренний IP-адрес (10.0.0.2) будет преобразовываться в указанный внешний IP-адрес (212.100.100.2).
10.0.0.2: Это внутренний IP-адрес, который принадлежит устройству внутри локальной сети.
212.100.100.2: Это внешний IP-адрес, который будет использоваться для связи с интернетом или другими внешними сетями.
**Пример**:
Если устройство с IP-адресом 10.0.0.2 пытается подключиться к интернету, его внутренний адрес будет заменён на 212.100.100.2. Это позволяет устройству быть видимым в интернете под внешним IP-адресом, сохраняя при этом его внутренний адрес скрытым. Удобненько.

## Настройка R2
```
Router>enable ! Переход в привилегированный режим (enable mode)
Router#configure terminal ! Вход в режим глобальной конфигурации
Router(config)#hostname R2 ! Установка имени устройства на R2
R2(config)#interface FastEthernet0/0 ! Выбор интерфейса FastEthernet 0/0 для настройки
R2(config-if)#ip address 212.100.100.1 255.255.255.0 ! Назначение IP-адреса 212.100.100.1 с маской 255.255.255.0
R2(config-if)#no shutdown ! Активация интерфейса (включение порта)
R2(config-if)#end ! Выход из режима конфигурации
R2#copy running-config startup-config ! Сохранение конфигурации
R2(config)#no ip routing ! Отключаем функцию маршрутизации на устройстве
R2(config)#ip default-gateway 212.100.100.2 ! Устанавливаем шлюз по умолчанию
```

## Итог

После настройки всего - идем пинговать R0 и R2 между собой. Это проход. 
Потом идем смотреть таблицу транляций и статистику. Это команды `show ip nat translations` и `show ip nat statistics` на R1.  
<img width="1412" height="591" alt="image" src="https://github.com/user-attachments/assets/2bd6538a-b2ca-4bcc-89db-d7108934108b" />

