# Настраиваем SSH доступ на примере коммутатора Cisco 2960-24

К сожалению, на данный момент, у меня под рукой только коммутатор CISCO 3550-24, который управляется IOS 12.2(55) вроде и не поддерживает современную версию SSH (v2), но об этом в следующем посте.

## Стартовая позиция.

Имеем примерно старую нашу схему.

![image](https://github.com/user-attachments/assets/f3d38e27-4285-4e96-a26d-432d1aa97b1a)

Ну в PocketTracer`е, конечно, вариантов поменьше (по железкам). Поэтому имеем вот такое: 
![image](https://github.com/user-attachments/assets/03ffdd3e-0ab7-45b3-9481-2baaef4405a2)

Поехали) 

- SW1> enable !Переходим в привелигированный режим
- SW1# conf t !Переходим в режиим глобальной конфигурации
- SW1(config)# hostname SW1 !Задаем имя коммуатора. Хотя в нашем случае оно уже задано. Я просто для примера.
- SW1(config)#ip domain-name iosif.local !задаем доменное имя
- SW1(config)# crypto key generate rsa general-keys modulus 2048 !Генерируем криптографический ключ для аутентификации
- SW1(config)# username admin privilege 15 secret cisco !Создаем локального пользователя с максимальным (15) уровнем прав
- SW1(config)# line vty 0 15 !Переходим к настройке терминальный линий VTY
- SW1(config-line)# transport input ssh !Переклачаем на доступ только по SSH (до этого было только по telnet)
- SW1(config-line)# login local !Включаем лкоальную аутентификацию
- SW1(config-line)# exit
- SW1(config)# ip ssh version 2 !Включаем SSH v2 (более безоопасный)
- SW1(config)# ip ssh time-out 60 !Настраиваем таймаут неактивности (60 секунд)
- SW1(config)# ip ssh authentication-retries 3 !Настраиваем максимальное количество попыток ввода пароля
