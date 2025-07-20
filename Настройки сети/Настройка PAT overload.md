# Настройка PAT Overload 

## Топология

<img width="1303" height="797" alt="image" src="https://github.com/user-attachments/assets/2afd5b73-3388-4366-830b-9326edfabc0d" />

Левая часть - внутренняя сеть.
Права часть - внешняя сеть. 

Как вы можете видеть, настроены 3-и разных VLAN (инструкция) и DHCP (инструкция)

Правай часть настроена статически. 

## Сама настройка

Сама настройка далась мне нелегко, т.к. я еще недостаточно опытен. 

Основная настройка здесь заключается в работе с роутером R1. 

А если конкретно:

```
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 202.130.2.1 255.255.255.0  ! Настройка внешнего интерфейса с публичным IP-адресом
Router(config-if)# no shutdown  ! Включение интерфейса
Router(config-if)# ip nat outside ! Помечаем интерфейс как внешний для NAT

Router(config)# interface FastEthernet1/0.10
Router(config-subif)# ip nat inside  ! Помечаем интерфейс как внутренний для NAT

Router(config)# interface FastEthernet1/0.20
Router(config-subif)# ip nat inside  ! Помечаем интерфейс как внутренний для NAT

Router(config)# interface FastEthernet2/0.30
Router(config-subif)# ip nat inside  ! Помечаем интерфейс как внутренний для NAT

Router(config)# access-list 100 permit 192.168.10.0 0.0.0.255  ! Разрешаем весь трафик из подсети 192.168.10.0/24
Router(config)# access-list 100 permit 192.168.20.0 0.0.0.255  ! Разрешаем весь трафик из подсети 192.168.20.0/24
Router(config)# access-list 100 permit 192.168.30.0 0.0.0.255  ! Разрешаем весь трафик из подсети 192.168.30.0/24

Router(config)# ip nat inside source list 100 interface FastEthernet0/0 overload  ! Настройка PAT:
                                                                                 ! - Используем ACL 100 для определения внутренних сетей
                                                                                 ! - Трансляция через интерфейс FastEthernet0/0
                                                                                 ! - Параметр overload включает PAT (many-to-one NAT)
```

Если я верно все понял, коллеги, то настройка верная, но честно - сильно запутался. 

## Проверка 
По какой то причине проверка на роутере через ```show ip nat translation``` вообще ничего не показывает.

PING проходит как надо, но вообще не понятно, работает все или нет. Возникает вопрос, как тогда проверить, что оно вообще хоть как то проверить? 

Я выкрутился за счет команды ``` show ip nat statisctics ````. 

Вывод команды - ниже: 
<img width="915" height="340" alt="image" src="https://github.com/user-attachments/assets/be723092-879c-44e4-8f2d-e4342866a346" />
Что она выводит? Ну давайте для удобства я попрошу ИИ описать: 

**Общая информация**
- Всего активных трансляций: 5
- - 0 статических
- - 5 динамических
- - 5 расширенных (extended)

**Конфигурация интерфейсов**
- Внешние интерфейсы (Outside):
- - FastEthernet0/0


- Внутренние интерфейсы (Inside):
- - FastEthernet1/0.10
- - FastEthernet1/0.20
- - FastEthernet2/0.30

**Статистика производительности**
- Hits (успешные трансляции): 50
- Misses (неудачные трансляции): 0
- CEF Translated packets: 50 (все пакеты успешно транслированы через CEF)
- CEF Punted packets: 0 (нет пакетов, требующих дополнительной обработки)
- Expired translations: 20 (количество истекших динамических трансляций)

**Динамические маппинги**
- Inside Source настроен через:
- - Access-list 100
- - Интерфейс FastEthernet0/0
- - Refcount: 5 (количество активных сессий)

**Статистика NAT-лимита**
- max entry:
- - максимально разрешено: 0 (лимит не установлен)
- - использовано: 0
- - пропущено: 0


Собственно, друзья, я так понимаю, что все работает. Если что то не так - прошу дать обратную связь мне в тлг: https://t.me/hanlaoyt
