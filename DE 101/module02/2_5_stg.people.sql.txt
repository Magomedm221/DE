-- stg.people definition

-- Drop table

-- DROP TABLE stg.people;

CREATE TABLE stg.people (
	person varchar(17) NOT NULL,
	region varchar(7) NOT NULL,
	CONSTRAINT people_pkey PRIMARY KEY (person)
);

INSERT INTO stg.people (person,region) VALUES
	 ('Anna Andreadi','West'),
	 ('Chuck Magee','East'),
	 ('Kelly Williams','Central'),
	 ('Cassandra Brandow','South');
