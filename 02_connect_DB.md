## Connect DB ##

knex.js.org

To install knex and postgre in gitBash terminal for server.js:

```
npm install knex

npm install PG

```
import to server.js like this: 
```
const knex = require ('knex') 
({
  client: 'mysql',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
});
```
Modify to put relevant data in: 
```
const knex = require ('knex') 
knex({
  client: 'pg',
  connection: {
    host : '127.0.0.1',
    user : 'rhamilton',
    password : '',
    database : 'smartbrain'
  }
});
```
ip address is default for localhots/home. 

```
npm start
```

Then set as variable and do postgres request to variable:

```
const knex = require ('knex') 


const postgres  = knex({
  client: 'pg',
  connection: {
    host : '127.0.0.1',
    user : 'rhamilton',
    password : '',
    database : 'smartbrain'
  }
});

console.log(postgres.select('*').from('users'));
```

This gives data in the console that indicates we are connected to smartbrain DB.
