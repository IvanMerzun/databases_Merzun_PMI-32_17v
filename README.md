<h1 name="content" align="center"><a href="">
</a> MSSQL</h1>

<p align="center">
  <a href="#-lab1"><img alt="lab1" src="https://img.shields.io/badge/Lab1-blue"></a>
  <a href="#-lab2"><img alt="lab2" src="https://img.shields.io/badge/Lab2-blue"></a> 
</p>
<h3 align="center">
  <a href="#client"></a>
  Вариант 17. Нотариальная контора.
  
Работа нотариальной конторы заключается в предоставлении клиентам комплекса юридических услуг. Каждая услуга имеет свою стоимость. Клиент (ФИО, адрес, телефон, вид деятельности) заключает с конторой сделку на предоставление некоторого набора услуг.
Информация о сделках: набор услуг, общая сумма сделки, комиссионные (доход конторы). 

Реализовать:
- Ведение прайс-листа услуг;
- Заключение сделок с клиентами,  расчет суммы сделки и комиссионных;
- Вывод постоянных клиентов и общих сумм сделок;
- Подсчет выручки конторы за заданный период.


</h3>

# <img src="https://github.com/user-attachments/assets/e080adec-6af7-4bd2-b232-d43cb37024ac" width="20" height="20"/> Lab1
[Назад](#content)
<h3 align="center">
  <a href="#client"></a>
  Разработать ER-модель данной предметной области: выделить сущности, их атрибуты, связи между сущностями. 
Для каждой сущности указать ее имя, атрибут (или набор атрибутов), являющийся первичным ключом, список остальных атрибутов.
Для каждого атрибута указать его тип, ограничения, может ли он быть пустым, является ли он первичным ключом.
Для каждой связи между сущностями указать: 
- тип связи (1:1, 1:M, M:N)
- обязательность

ER-модель д.б. представлена в виде ER-диаграммы (картинка)

По имеющейся ER-модели создать реляционную модель данных и отобразить ее в виде списка сущностей с их атрибутами и типами атрибутов,  для атрибутов указать, явл. ли он первичным или внешним ключом 
</h3>

#### №1. ER-модель
![image](/pictures/ERm.png)

#### №1.1. Реляционная модель
![image](/pictures/Rel.png)


# <img src="https://github.com/user-attachments/assets/e080adec-6af7-4bd2-b232-d43cb37024ac" width="20" height="20"/> Lab2
[Назад](#content)
<h3 align="center">
  <a href="#client"></a>
  В соответствии с реляционной моделью данных, разработанной в Лаб.№1, создать реляционную БД на учебном сервере БД:
  
- создать таблицы, определить первичные ключи и иные ограничения
  
- определить связи между таблицами
  
- создать диаграмму

- заполнить все таблицы адекватной информацией (не меньше 10 записей в таблицах, наличие примеров для связей типа 1:M )

</h3>

## №2. Создание таблиц
```
USE [base_var17]

DROP TABLE IF EXISTS Deal_Service;
DROP TABLE IF EXISTS Service_PriceList;
DROP TABLE IF EXISTS Deal;
DROP TABLE IF EXISTS Client;
DROP TABLE IF EXISTS Notary;
DROP TABLE IF EXISTS Services;
DROP TABLE IF EXISTS PriceList;

CREATE TABLE Client (
    client_id INT PRIMARY KEY IDENTITY,
    full_name NVARCHAR(100) NOT NULL,
    phone NVARCHAR(10) NULL CHECK (phone LIKE '[0-9]%'),
    addres NVARCHAR(300) NULL,
    activity_type NVARCHAR(300) NULL
);


CREATE TABLE Notary (
    notary_id INT PRIMARY KEY IDENTITY,
    full_name NVARCHAR(100) NOT NULL,
    phone NVARCHAR(10) NULL CHECK (phone LIKE '[0-9]%'),
    email NVARCHAR(50) NULL CHECK (email LIKE '%@%.__%'),
    job_title NVARCHAR(100) NOT NULL
);

CREATE TABLE Deal (
    deal_id INT PRIMARY KEY IDENTITY,
    deal_number INT NOT NULL UNIQUE,
    deal_date DATE NOT NULL DEFAULT GETDATE(),
    total_amount DECIMAL(10,2) NOT NULL CHECK (total_amount >= 0),
    commission DECIMAL(10,2) NOT NULL CHECK (commission >= 0),
    deal_status NVARCHAR(20) NOT NULL DEFAULT 'В обработке',
    client_id INT NOT NULL,
    notary_id INT NOT NULL,
    FOREIGN KEY (client_id) REFERENCES Client(client_id),
    FOREIGN KEY (notary_id) REFERENCES Notary(notary_id)
);



CREATE TABLE Services (
    service_id INT PRIMARY KEY IDENTITY,
    service_name NVARCHAR(100) NOT NULL,
    cost DECIMAL(10,2) NOT NULL CHECK (cost >= 0),
    description NVARCHAR(300) NULL
);

CREATE TABLE PriceList (
    pricelist_id INT PRIMARY KEY IDENTITY,
    pricelist_name NVARCHAR(100) NOT NULL,
    currency NVARCHAR(10) NOT NULL DEFAULT 'RUB',
    date_start DATE NOT NULL DEFAULT GETDATE(),
    date_end DATE NULL,
    comment NVARCHAR(300) NULL,
    CHECK (date_end IS NULL OR date_end >= date_start)
);


CREATE TABLE Deal_Service (
    deal_id INT NOT NULL,
    service_id INT NOT NULL,
    PRIMARY KEY (deal_id, service_id),
    FOREIGN KEY (deal_id) REFERENCES Deal(deal_id),
    FOREIGN KEY (service_id) REFERENCES Services(service_id)
);


CREATE TABLE Service_PriceList (
    service_id INT NOT NULL,
    pricelist_id INT NOT NULL,
    PRIMARY KEY (service_id, pricelist_id),
    FOREIGN KEY (service_id) REFERENCES Services(service_id),
    FOREIGN KEY (pricelist_id) REFERENCES PriceList(pricelist_id)
);

INSERT INTO Client (full_name, phone, addres, activity_type)
VALUES 
(N'Иванов Иван Иванович', '8900123456', N'Москва, ул. Ленина, д.1', N'Физическое лицо'),
(N'Петров Петр Петрович', '8900234567', N'Санкт-Петербург, Невский пр., д.10', N'Физическое лицо'),
(N'Сидорова Анна Николаевна', '8900345678', N'Казань, ул. Кремлёвская, д.5', N'ИП'),
(N'Кузнецов Алексей Павлович', '8900456789', N'Новосибирск, Красный пр., д.20', N'Юридическое лицо'),
(N'Морозова Ольга Сергеевна', '8900567890', N'Екатеринбург, ул. Мира, д.15', N'Физическое лицо'),
(N'Васильев Николай Олегович', '8900678901', N'Нижний Новгород, ул. Горького, д.12', N'ИП'),
(N'Смирнов Дмитрий Алексеевич', '8900789012', N'Ростов-на-Дону, ул. Пушкинская, д.8', N'Юридическое лицо'),
(N'Федорова Елена Викторовна', '8900890123', N'Самара, ул. Молодогвардейская, д.3', N'Физическое лицо'),
(N'Михайлов Артем Андреевич', '8900901234', N'Уфа, ул. Октября, д.11', N'Физическое лицо'),
(N'Григорьев Сергей Валентинович', '8901012345', N'Краснодар, ул. Северная, д.9', N'Юридическое лицо');

INSERT INTO Notary (full_name, phone, email, job_title)
VALUES
(N'Иванова Мария Сергеевна', '8901111222', 'ivanova@mail.ru', N'Главный нотариус'),
(N'Семенов Павел Викторович', '8901111333', 'semenov@mail.ru', N'Нотариус'),
(N'Орлова Екатерина Петровна', '8901111444', 'orlova@mail.ru', N'Нотариус'),
(N'Киселев Игорь Николаевич', '8901111555', 'kiselev@mail.ru', N'Нотариус'),
(N'Алексеева Оксана Дмитриевна', '8901111666', 'alekseeva@mail.ru', N'Нотариус'),
(N'Попов Андрей Иванович', '8901111777', 'popov@mail.ru', N'Нотариус'),
(N'Соколова Татьяна Олеговна', '8901111888', 'sokolova@mail.ru', N'Нотариус'),
(N'Захаров Владимир Степанович', '8901111999', 'zakharov@mail.ru', N'Нотариус'),
(N'Калинина Наталья Игоревна', '8901112000', 'kalinina@mail.ru', N'Нотариус'),
(N'Громов Сергей Владимирович', '8901112111', 'gromov@mail.ru', N'Нотариус');

INSERT INTO Services (service_name, cost, description)
VALUES
(N'Заверение копий документов', 500, N'Заверение копий официальных документов'),
(N'Заверение подписи', 700, N'Заверение подписи физического или юридического лица'),
(N'Составление доверенности', 1500, N'Подготовка и заверение доверенности'),
(N'Составление завещания', 2000, N'Оформление и заверение завещания'),
(N'Составление брачного договора', 2500, N'Подготовка и заверение брачного договора'),
(N'Регистрация сделок с недвижимостью', 5000, N'Юридическое сопровождение сделок с недвижимостью'),
(N'Оформление наследства', 3000, N'Консультации и заверение документов по наследству'),
(N'Заверение перевода документа', 1000, N'Проверка и заверение перевода на иностранный язык'),
(N'Заверение договора аренды', 3500, N'Оформление и заверение договора аренды'),
(N'Оформление доверенности для бизнеса', 4000, N'Подготовка доверенности для юридических лиц');

INSERT INTO PriceList (pricelist_name, currency, date_start, date_end, comment)
VALUES
(N'Прайс январь 2024', 'RUB', '2024-01-01', '2024-01-31', N'Тарифы января'),
(N'Прайс февраль 2024', 'RUB', '2024-02-01', '2024-02-29', N'Тарифы февраля'),
(N'Прайс март 2024', 'RUB', '2024-03-01', '2024-03-31', N'Тарифы марта'),
(N'Прайс апрель 2024', 'RUB', '2024-04-01', '2024-04-30', N'Тарифы апреля'),
(N'Прайс май 2024', 'RUB', '2024-05-01', '2024-05-31', N'Тарифы мая'),
(N'Прайс июнь 2024', 'RUB', '2024-06-01', '2024-06-30', N'Тарифы июня'),
(N'Прайс июль 2024', 'RUB', '2024-07-01', '2024-07-31', N'Тарифы июля'),
(N'Прайс август 2024', 'RUB', '2024-08-01', '2024-08-31', N'Тарифы августа'),
(N'Прайс сентябрь 2024', 'RUB', '2024-09-01', '2024-09-30', N'Тарифы сентября'),
(N'Прайс октябрь 2024', 'RUB', '2024-10-01', '2024-10-31', N'Тарифы октября');

INSERT INTO Deal (deal_number, deal_date, total_amount, commission, deal_status, client_id, notary_id)
VALUES
(1001, '2024-01-05', 500, 50, N'Завершена', 1, 1),
(1002, '2024-01-10', 700, 70, N'Завершена', 2, 2),
(1003, '2024-02-12', 1500, 150, N'Завершена', 3, 3),
(1004, '2024-03-01', 2000, 200, N'В обработке', 4, 4),
(1005, '2024-03-15', 2500, 250, N'Завершена', 5, 5),
(1006, '2024-04-20', 5000, 500, N'Завершена', 6, 6),
(1007, '2024-05-03', 3000, 300, N'Отменена', 7, 7),
(1008, '2024-06-10', 1000, 100, N'Завершена', 8, 8),
(1009, '2024-07-08', 3500, 350, N'Завершена', 9, 9),
(1010, '2024-08-25', 4000, 400, N'В обработке', 10, 10);

INSERT INTO Deal_Service (deal_id, service_id)
VALUES
(1, 1), (1, 2),
(2, 3),
(3, 4),
(4, 5), (4, 6),
(5, 7),
(6, 8), (6, 9),
(7, 10),
(8, 1),
(9, 2), (9, 3),
(10, 4), (10, 5);

INSERT INTO Service_PriceList (service_id, pricelist_id)
VALUES
(1,1),(2,1),(3,1),(4,1),(5,1),(6,1),(7,1),(8,1),(9,1),(10,1),
(1,2),(2,2),(3,2),(4,2),(5,2),(6,2),(7,2),(8,2),(9,2),(10,2),
(1,3),(2,3),(3,3),(4,3),(5,3),(6,3),(7,3),(8,3),(9,3),(10,3),
(1,4),(2,4),(3,4),(4,4),(5,4),(6,4),(7,4),(8,4),(9,4),(10,4),
(1,5),(2,5),(3,5),(4,5),(5,5),(6,5),(7,5),(8,5),(9,5),(10,5);


SELECT * FROM Client
SELECT * FROM Notary
SELECT * FROM Services
SELECT * FROM PriceList
SELECT * FROM Deal
SELECT * FROM Deal_Service
SELECT * FROM Service_PriceList



```
## №2.1. Диаграмма
![image](/pictures/Diagr.png)

## №2.2. Заполненные таблицы
### Клиент
![image](/pictures/Client.png)
### Нотариус
![image](/pictures/Not.png)
### Услуга
![image](/pictures/Ysl.png)
### Прайс-лист
![image](/pictures/Prlist.png)
### Сделка
![image](/pictures/Sdel.png)
### Сделка_услуга
![image](/pictures/Sd_ys.png)
### Услуга_лист
![image](/pictures/Ys_list.png)
