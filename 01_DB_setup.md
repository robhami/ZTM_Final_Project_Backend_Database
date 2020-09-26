## Setting up database ##

From terminal:
```
C:\Users\Rob.000>psql -U postgres


postgres=# CREATE DATABASE smartbrain;
CREATE DATABASE

postgres=# \c smartbrain
WARNING: Console code page (437) differs from Windows code page (1252)
         8-bit characters might not work correctly. See psql reference
         page "Notes for Windows users" for details.
You are now connected to database "smartbrain" as user "postgres".
smartbrain=#
```
In postGres interface: 
```
CREATE TABLE users (
	id serial PRIMARY KEY,
	name VARCHAR(100),
	email text UNIQUE NOT NULL,
	entries BIGINT DEFAULT 0,
	joined TIMESTAMP NOT NULL
);
```
Then do: 
```
CREATE TABLE login (
	id serial PRIMARY KEY,
	hash varchar(100) NOT NULL,
	email text UNIQUE NOT NULL
	);
 ```
 
