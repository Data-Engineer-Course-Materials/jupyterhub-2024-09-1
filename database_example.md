```sql
DROP DATABASE if exists tg;
CREATE DATABASE tg;
USE tg;
DROP TABLE IF EXISTS Export;
DROP TABLE IF EXISTS Client_Order;
DROP TABLE IF EXISTS Import;
DROP TABLE IF EXISTS Supplier_Order;
DROP TABLE IF EXISTS Facture;
DROP TABLE IF EXISTS Product;
DROP TABLE IF EXISTS Shipper;
DROP TABLE IF EXISTS Client;
DROP TABLE IF EXISTS Supplier;
DROP TABLE IF EXISTS Contact;

CREATE TABLE if not exists Contact (
	Id bigserial primary key,
	Name text,
	Phone text,
	Email text
);

CREATE TABLE if not exists Supplier(
	Id bigint primary key CONSTRAINT FK_Supplier_Contact REFERENCES Contact(id),
	Deposit_Eur decimal
);


CREATE TABLE if not exists Client(
	Id bigint primary key CONSTRAINT FK_Client_Contact REFERENCES Contact(id),
	Deposit_Vnd decimal,
	Category text
);


CREATE TABLE if not exists Shipper(
	Id bigint primary key CONSTRAINT FK_Shipper_Contact REFERENCES Contact(id)
);


CREATE TABLE if not exists Product(
	Id bigserial primary key,	
	Code text unique,
	Name_Fr text,
	Name_Vn text,
	Weight numeric(9,4),
	Comment text,
	Archived boolean not null DEFAULT false
);


----------------

CREATE TABLE if not exists Facture(
	Id bigserial primary key,
	Supplier_Id bigint not null CONSTRAINT FK_Facture_Supplier REFERENCES Supplier(Id),
	--
	Num text,
	Date timestamp DEFAULT CURRENT_TIMESTAMP,
	vnd_per_eur numeric(9,2), --Ty Price VND/EUR
	Comment text,
	Archived boolean not null DEFAULT false
);


CREATE TABLE if not exists Supplier_Order(
	Id bigserial primary key,
	Supplier_Id bigint not null CONSTRAINT FK_Supplier_Order_Supplier REFERENCES Supplier(Id),
	Product_Id bigint not null REFERENCES Product(Id),
	--
	Price_Eur decimal not null,
	Qty int not null,
	Sent_Date timestamp not null DEFAULT CURRENT_TIMESTAMP,
	Comment text,
	Archived boolean not null DEFAULT false
);


CREATE TABLE if not exists Import(
	Id bigserial primary key,
	Facture_Id bigint  not null CONSTRAINT FK_Import_Facture REFERENCES Facture(Id),
	Product_Id bigint not null CONSTRAINT FK_Import_Product REFERENCES Product(Id),
	Shipper_Id bigint CONSTRAINT FK_Import_Shipper REFERENCES Shipper(Id), --nullable
	Supplier_Order_Id bigint CONSTRAINT FK_Import_Supplier_Order REFERENCES Supplier_Order(Id), --nullable
	--
	Date timestamp DEFAULT CURRENT_TIMESTAMP not null,
	Price_Eur_On_Facture decimal not null,
	Qty_On_Facture int,
	Qty_Real int not null,
	ship_price_eurperkg decimal DEFAULT 0 not null,
	Comment text,
	Archived boolean not null DEFAULT false,
	CONSTRAINT UL_Import_Facture_Product UNIQUE (Facture_Id, Product_Id)
);


CREATE TABLE if not exists Client_Order(
	Id bigserial primary key,
	Client_Id bigint not null CONSTRAINT FK_Client_Order_Client REFERENCES Client(Id),
	Product_Id bigint not null CONSTRAINT FK_Client_Order_Product REFERENCES Product(Id),
	--
	Agreement_Price_Vnd decimal not null,
	Qty int not null,
	Date timestamp DEFAULT CURRENT_TIMESTAMP,
	Paid_Vnd decimal DEFAULT 0,
	Comment text,
	Archived boolean not null DEFAULT false,
	constraint ClientOverPaid check (Paid_Vnd <= Agreement_Price_Vnd*Qty)
);


CREATE TABLE if not exists Export (
	Id bigserial primary key,
	Client_Order_Id bigint not null CONSTRAINT FK_Export_Client_Order REFERENCES Client_Order(Id),
	Shipper_Id bigint CONSTRAINT FK_Export_Shipper REFERENCES Shipper(Id),
	--
	Date timestamp not null DEFAULT CURRENT_TIMESTAMP,
	Qty int not null,
	Price_Vnd decimal not null,
	Shipped boolean not null DEFAULT false,
	Ship_Price_VndPerKg decimal,
	Comment text,
	Archived boolean not null DEFAULT false
);

-- fill Numme fictive data
INSERT INTO Contact(Id, Name, Phone, Email) VALUES (1, 'Olivier', '123456', 'olivier@email.com');
INSERT INTO Supplier(Id) VALUES (1);
INSERT INTO Contact(Id, Name, Phone, Email) VALUES (2, 'Catherine', '123456', 'catherine@email.com');
INSERT INTO Supplier(Id) VALUES (2);

INSERT INTO Contact(Id, Name, Phone, Email) VALUES (3, 'Dung', '123456', 'dung@email.com');
INSERT INTO Client(Id, Category) VALUES (3, 'kl');
INSERT INTO Contact(Id, Name, Phone, Email) VALUES (4, 'Minh', '123456', 'minh@email.com');
INSERT INTO Client(Id, Category) VALUES (4, 'kl');
INSERT INTO Contact(Id, Name, Phone, Email) VALUES (5, 'Cute Shop', '123456', 'cuteshop@email.com');
INSERT INTO Client(Id, Category, Deposit_Vnd) VALUES (5, 'lb', 12500000);
INSERT INTO Contact(Id, Name, Phone, Email) VALUES (6, 'Miss Shop', '123456', 'misshop@email.com');
INSERT INTO Client(Id, Category, Deposit_Vnd) VALUES (6, 'kb', 15000000);

INSERT INTO Contact(Id, Name, Phone, Email) VALUES (7, 'Fedex', '123456', 'fedex@email.com');
INSERT INTO Shipper(Id) VALUES (7);
INSERT INTO Contact(Id, Name, Phone, Email) VALUES (8, 'LaPost', '123456', 'lapost@email.com');
INSERT INTO Shipper(Id) VALUES (8);

INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (1, 'BIOC001', 'BIODERMA Créaline AR BB Cream clair, 40ml', 'BIODERMA Créaline AR BB Cream clair, 40ml', 0.2);
INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (2, 'VICH823', 'VICHY Démaquillant Waterproof yeux', 'VICHY Démaquillant Waterproof yeux', 0.22);
INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (3, 'URIA001', 'URIAGE BARIÉSUN Lait Numyeux Autobronzant, 100', 'URIAGE BARIÉSUN Lait Numyeux Autobronzant, 100', 0.19);
INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (4, 'CAUD002', 'CAUDALIE Mousse Nettoyante Fleur de Vigne DUO, 2x', 'CAUDALIE Mousse Nettoyante Fleur de Vigne DUO, 2x', 0.3);
INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (5, 'BIOD005', 'BIODERMA Créaline H2O, 250ml', 'BIODERMA Créaline H2O, 250ml', 0.18);
INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (6, 'MUST119', 'MUSTELA Gel Lavant Doux, 500ml', 'MUSTELA Gel Lavant Doux, 500ml', 0.19);
INSERT INTO Product(Id, Code, Name_Fr, Name_Vn, Weight) VALUES (7, 'VICH383', 'VICHY Aqualia Thermal effet SPA Numin de nuit, 75 ml', 'VICHY Aqualia Thermal effet SPA Numin de nuit, 75 ml', 0.21);

INSERT INTO Supplier_Order(Id, Supplier_Id, Product_Id, Price_Eur, Qty) VALUES (1, 1, 1, 3.20, 300);
INSERT INTO Supplier_Order(Id, Supplier_Id, Product_Id, Price_Eur, Qty) VALUES (2, 2, 1, 3.10, 400);
INSERT INTO Supplier_Order(Id, Supplier_Id, Product_Id, Price_Eur, Qty) VALUES (3, 2, 2, 2.10, 300);
INSERT INTO Supplier_Order(Id, Supplier_Id, Product_Id, Price_Eur, Qty) VALUES (4, 2, 3, 3.20, 200);
INSERT INTO Supplier_Order(Id, Supplier_Id, Product_Id, Price_Eur, Qty) VALUES (5, 1, 3, 3.15, 200);
INSERT INTO Supplier_Order(Id, Supplier_Id, Product_Id, Price_Eur, Qty) VALUES (6, 1, 4, 3.15, 100);

INSERT INTO Facture(Id, Supplier_Id, Num, vnd_per_eur) VALUES (1, 1, 'MONGE73653235', 27300);
INSERT INTO Facture(Id, Supplier_Id, Num, vnd_per_eur) VALUES (2, 2, 'CHATEL3838399', 27300);

INSERT INTO Import(Id, Facture_Id, Product_Id, Shipper_Id, Price_Eur_On_Facture, Qty_On_Facture, Qty_Real, Ship_Price_EurPerKg) 
	VALUES (1, 1, 1, 7, 3.20, 300, 290, 8);
INSERT INTO Import(Id, Facture_Id, Product_Id, Shipper_Id, Price_Eur_On_Facture, Qty_On_Facture, Qty_Real, Ship_Price_EurPerKg) 
	VALUES (2, 1,  4, 7, 3.17, 100, 105, 8);
INSERT INTO Import(Id, Facture_Id, Product_Id, Shipper_Id, Price_Eur_On_Facture, Qty_On_Facture, Qty_Real, Ship_Price_EurPerKg) 
	VALUES (3, 2,  1, 8, 3.10, 400, 400, 9);
INSERT INTO Import(Id, Facture_Id, Product_Id, Shipper_Id, Price_Eur_On_Facture, Qty_On_Facture, Qty_Real, Ship_Price_EurPerKg) 
	VALUES (4, 2,  2, 8, 2.15, 250, 240, 9);
INSERT INTO Import(Id, Facture_Id, Product_Id, Shipper_Id, Price_Eur_On_Facture, Qty_On_Facture, Qty_Real, Ship_Price_EurPerKg) 
	VALUES (5, 2,  3, 8, 3.20, 150, 160, 9);

INSERT into Client_Order(Id, Client_Id, Product_Id, Agreement_Price_Vnd, Qty, Paid_Vnd) 
	VALUES (1, 3, 1, 120000, 50, 4200000);
INSERT into Client_Order(Id, Client_Id, Product_Id, Agreement_Price_Vnd, Qty) 
	VALUES (2, 3, 4, 125000, 60);
INSERT into Client_Order(Id, Client_Id, Product_Id, Agreement_Price_Vnd, Qty) 
	VALUES (3, 4, 1, 130000, 40);

INSERT into Export(Id, Client_Order_Id, Price_Vnd, Qty) VALUES (1, 1, 120000, 3);
INSERT into Export(Id, Client_Order_Id, Price_Vnd, Qty) VALUES (2, 2, 150000, 6);
INSERT into Export(Id, Client_Order_Id, Price_Vnd, Qty) VALUES (3, 3, 130000, 3);
```
