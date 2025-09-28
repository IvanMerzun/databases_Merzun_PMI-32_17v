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

DROP TABLE IF EXISTS Сделка_услуга;
DROP TABLE IF EXISTS Услуга_лист;
DROP TABLE IF EXISTS Сделка;
DROP TABLE IF EXISTS Клиент;
DROP TABLE IF EXISTS Нотариус;
DROP TABLE IF EXISTS Услуга;
DROP TABLE IF EXISTS Прайс_лист;

CREATE TABLE Клиент (
    ID_клиента INT PRIMARY KEY IDENTITY,
    ФИО NVARCHAR(100) NOT NULL,
    Телефон NVARCHAR(10) NULL CHECK (Телефон LIKE '[0-9]%'),
    Адрес NVARCHAR(300) NULL,
    Вид_деятельности NVARCHAR(300) NULL
);


CREATE TABLE Нотариус (
    ID_нотариуса INT PRIMARY KEY IDENTITY,
    ФИО NVARCHAR(100) NOT NULL,
    Телефон NVARCHAR(10) NULL CHECK (Телефон LIKE '[0-9]%'),
    Эл_почта NVARCHAR(50) NULL CHECK (Эл_почта LIKE '%@%.__%'),
    Должность NVARCHAR(100) NOT NULL
);

CREATE TABLE Сделка (
    ID_сделки INT PRIMARY KEY IDENTITY,
    Номер_сделки INT NOT NULL UNIQUE,
    Дата_сделки DATE NOT NULL DEFAULT GETDATE(),
    Общая_сумма DECIMAL(10,2) NOT NULL CHECK (Общая_сумма >= 0),
    Комиссионные DECIMAL(10,2) NOT NULL CHECK (Комиссионные >= 0),
    Статус NVARCHAR(20) NOT NULL DEFAULT 'В обработке',
    ID_клиента INT NOT NULL,
    ID_нотариуса INT NOT NULL,
    FOREIGN KEY (ID_клиента) REFERENCES Клиент(ID_клиента),
    FOREIGN KEY (ID_нотариуса) REFERENCES Нотариус(ID_нотариуса)
);

CREATE TABLE Услуга (
    ID_услуги INT PRIMARY KEY IDENTITY,
    Название NVARCHAR(100) NOT NULL,
    Стоимость DECIMAL(10,2) NOT NULL CHECK (Стоимость >= 0),
    Описание NVARCHAR(300) NULL
);

CREATE TABLE Прайс_лист (
    ID_листа INT PRIMARY KEY IDENTITY,
    Название NVARCHAR(100) NOT NULL,
    Валюта NVARCHAR(10) NOT NULL DEFAULT 'RUB',
    Дата_начала DATE NOT NULL DEFAULT GETDATE(),
    Дата_окончания DATE NULL,
    Комментарий NVARCHAR(300) NULL,
    CHECK (Дата_окончания IS NULL OR Дата_окончания >= Дата_начала)
);


CREATE TABLE Сделка_услуга (
    ID_сделки INT NOT NULL,
    ID_услуги INT NOT NULL,
    PRIMARY KEY (ID_сделки, ID_услуги),
    FOREIGN KEY (ID_сделки) REFERENCES Сделка(ID_сделки),
    FOREIGN KEY (ID_услуги) REFERENCES Услуга(ID_услуги)
);


CREATE TABLE Услуга_лист (
    ID_услуги INT NOT NULL,
    ID_листа INT NOT NULL,
    PRIMARY KEY (ID_услуги, ID_листа),
    FOREIGN KEY (ID_услуги) REFERENCES Услуга(ID_услуги),
    FOREIGN KEY (ID_листа) REFERENCES Прайс_лист(ID_листа)
);

```
## №2.1. Диаграмма
![image](/pictures/Diag1.png)

## №2.2. Заполненные таблицы
### Клиент
![image](/pictures/cli.png)
### Нотариус
![image](/pictures/nomer.png)
### Услуга
![image](/pictures/bron.png)
### Прайс-лист
![image](/pictures/nombro.png)
### Сделка
![image](/pictures/zasel.png)
### Сделка_услуга
![image](/pictures/zasnom.png)
### Услуга_лист
![image](/pictures/yslygi.png)
