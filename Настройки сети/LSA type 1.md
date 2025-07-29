Для начала собираем короткую топологию

<img width="901" height="381" alt="image" src="https://github.com/user-attachments/assets/a85188f2-992f-4ce4-b770-904841a9d455" />

Быстреньо настраиваем простейший OSPF на два узла ([инструкция](https://github.com/iosif-tihonenkov/net_ing/blob/main/Настройки%20сети/Настройка%20OSPF.md))

Моя цель - посмотреть на LSA Type 1. 

Для этого я взял WireShark для f0/0 на R1. 
Настраиваем фильтр на OSPF и путем логики ищем в info графе - LS Aknowledge
<img width="991" height="495" alt="image" src="https://github.com/user-attachments/assets/e81bfb44-a74b-4bc2-a070-ae5b70b8a64c" />

Открываем подробнее и находим подробную информация об LSA Type 1


<img width="701" height="553" alt="image" src="https://github.com/user-attachments/assets/063fb477-5e27-403f-abf8-09bf2bb22340" />

Правда, интересный вопрос - почему их два? Честно - хз. 

Что мы видим? 
- Возраст LSA = 1 секунда
- Каналы по требованию - поддерживаются
- Внешняя маршрутизациия - поддерживается
- Остальное не поддерживается
- Тип LSA - 1
- Маршрутизатор, анонсирующий LSA - 192.168.2.1
- Порядовый номер - 0x80000003
- Контрольная сумм 0xb73
- Длина: 48

Так же, если взглянем повыше: 

<img width="543" height="380" alt="image" src="https://github.com/user-attachments/assets/591b04be-e6f6-4eb1-9dcc-a6cc63faa86f" />

То мы увидим следующую информацию: 
- Верия OSPF - 2
- Тип сообщения - LS Aknowledge
- Длина пакета: 64 байта
- Источник пакета: 192.168.1.1 (Он же Router-ID -  RID)

  Ну и вроде все. 

Кстати, вообще, хотелось бы увидеть одно из главных, что я хотел тут увидеть - COST маршрута. Поэтому я взял LS Update, который рассылался на широковещательный домен

<img width="669" height="183" alt="image" src="https://github.com/user-attachments/assets/62d9eb17-1901-4b68-bd79-469bbb8aee93" />

Заходим внутрь: 

<img width="617" height="368" alt="image" src="https://github.com/user-attachments/assets/081d059e-e152-4d29-8fcf-b2be331e3f18" />

То, что выделенно красным - Кол-во описанных сетей в данном LSA.
В графе "Metric" - мы видим стоимость (COST) маршрута.

