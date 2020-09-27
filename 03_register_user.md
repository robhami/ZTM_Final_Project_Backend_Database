

Add login details user and password for Postgres, then do promise and console.log data (also change const var to db):

```
const db  = knex({
  client: 'pg',
  connection: {
    host : '127.0.0.1',
    user : 'postgres',
    password : 'Ham&1974',
    database : 'smartbrain'
  }
});

db.select('*').from('users').then (data => {
	console.log(data);
});
```
 This will return array console running node: 
 ```
[nodemon] restarting due to changes...
[nodemon] starting `node server.js`
app is running on port 3000
[]
```

 REmove push function and use Insert from knex to insert values in database:
 
 ```
 app.post('/register',(req,res)=>{
	const {email, name, password} =req.body;
	bcrypt.hash(password, null, null, function(err, hash) {
    	console.log(hash);
	});

	db('users').insert ({
		email: email,
		name: name,
		joined: new Date()
		}).then	(console.log)

	// database.users.push({
	// 	id: '125', 
	// 	name: name, 
	// 	email: email,  
	// 	entries:0, 
	// 	joined: new Date()
	// })
	res.json(database.users[database.users.length-1]);
})
```
Can then post quickly by using postman and POST request:
```
localhost:3000/register
```
with the following in body as in earlier classes: 
```
{
	"email":"ann@gmail.com", 
	"password":"apples",
    "name":"Ann"

}
```
This returns: 
```
$2a$10$evfsJZIfZ1TwCz/YqaL/NOobiLCtfjXoiGvq./dhdscWEfUsZwWBm
Result {
  command: 'INSERT',
  rowCount: 1,
  oid: 0,
  rows: [],
  fields: [],
  _parsers: undefined,
  _types: TypeOverrides {
    _types: {
      getTypeParser: [Function: getTypeParser],
      setTypeParser: [Function: setTypeParser],
      arrayParser: [Object],
      builtins: [Object]
    },
    text: {},
    binary: {}
  },
  RowCtor: null,
  rowAsArray: false
}
```
Then in psql terminal do the following to check users DB: 
```
smartbrain=# SELECT * FROM users;
 id | name |     email     | entries |         joined
----+------+---------------+---------+-------------------------
  1 | Ann  | ann@gmail.com |       0 | 2020-09-27 22:11:20.333
(1 row)
```
