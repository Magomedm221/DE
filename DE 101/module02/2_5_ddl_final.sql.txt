create schema dw;

-- ************************************** customer_dim

CREATE TABLE dw.customer_dim
(
 customer_dim_id serial NOT NULL,
 customer_id     varchar(8) NOT NULL,
 customer_name   varchar(22) NOT NULL,
 segment         varchar(11) NOT NULL,
 CONSTRAINT PK_64 PRIMARY KEY ( customer_dim_id )
);


-- ************************************** geography_dim

CREATE TABLE dw.geography_dim
(
 geo_dim_id        serial NOT NULL,
 country           varchar(13) NOT NULL,
 city              varchar(17) NOT NULL,
 "state"             varchar(20) NOT NULL,
 region            varchar(7) NOT NULL,
 postal_code       varchar(20) NOT NULL,
 full_name_manager varchar(17) NOT NULL,
 CONSTRAINT PK_31 PRIMARY KEY ( geo_dim_id )
);



-- ************************************** product_dim

CREATE TABLE dw.product_dim
(
 product_dim_id serial NOT NULL,
 category       varchar(15) NOT NULL,
 subcategory    varchar(11) NOT NULL,
 product_name   varchar(127) NOT NULL,
 product_id     varchar(15) NOT NULL,
 CONSTRAINT PK_49 PRIMARY KEY ( product_dim_id )
);

-- ************************************** shipping_dim

CREATE TABLE dw.shipping_dim
(
 ship_dim_id serial NOT NULL,
 ship_mode   varchar(14) NOT NULL,
 CONSTRAINT PK_42 PRIMARY KEY ( ship_dim_id )
);


-- ************************************** order_date_dim

CREATE TABLE dw.order_date_dim
(
 order_date_dim_id serial NOT NULL,
 orders_date       date NOT NULL,
 year              integer NOT NULL,
 quarter           integer NOT NULL,
 month             integer NOT NULL,
 week              integer NOT NULL,
 day               integer NOT NULL,
 CONSTRAINT PK_15 PRIMARY KEY ( order_date_dim_id )
);

-- ************************************** ship_date_dim

CREATE TABLE dw.ship_date_dim
(
 ship_date_dim_id serial NOT NULL,
 ships_date       date NOT NULL,
 year             integer NOT NULL,
 quarter          integer NOT NULL,
 month            integer NOT NULL,
 week             integer NOT NULL,
 day              integer NOT NULL,
 CONSTRAINT PK_16 PRIMARY KEY ( ship_date_dim_id )
);

-- ************************************** sales_fact

CREATE TABLE dw.sales_fact
(
 row_id            serial NOT NULL,
 order_id          varchar(25) NOT NULL,
 sales             numeric(9,4) NOT NULL,
 profit            numeric(21,16) NOT NULL,
 quantity          integer NOT NULL,
 discount          numeric(4,2) NOT NULL,
 returned          varchar(3) NULL,
 geo_dim_id        serial NOT NULL,
 customer_dim_id   serial NOT NULL,
 ship_dim_id       serial NOT NULL,
 product_dim_id    serial NOT NULL,
 order_date_dim_id serial NOT NULL,
 ship_date_dim_id  serial NOT NULL,
 CONSTRAINT PK_7 PRIMARY KEY ( row_id ),
);

