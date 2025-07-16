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

```
