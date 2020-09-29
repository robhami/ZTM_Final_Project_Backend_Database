
KNEX has method called returning that below returns all columns then repalce response in then.response at bottom: 

```
app.post('/register',(req,res)=>{
	const {email, name, password} =req.body;
	bcrypt.hash(password, null, null, function(err, hash) {
    	console.log(hash);
	});

	db('users')
		.returning('*')
		.insert ({
		email: email,
		name: name,
		joined: new Date()
		})
		.then (response => {
			res.json(response);
		})	
})
```
In Postman change body to Anne with an E: 

```
{
	"email":"anne@gmail.com", 
	"password":"apples",
    "name":"Anne"
}
```
This return reponse in Postman of an array with Anne in it: 

```
[
    {
        "id": 2,
        "name": "Anne",
        "email": "anne@gmail.com",
        "entries": "0",
        "joined": "2020-09-28T14:37:23.064Z"
    }
]
```
Then in psql:
```
smartbrain=# SELECT * FROM users;
 id | name |     email      | entries |         joined
----+------+----------------+---------+-------------------------
  1 | Ann  | ann@gmail.com  |       0 | 2020-09-27 22:11:20.333
  2 | Anne | anne@gmail.com |       0 | 2020-09-28 18:37:23.064
(2 rows)
```
Can add another user same way: 
```
smartbrain=# SELECT * FROM users;
 id | name |     email      | entries |         joined
----+------+----------------+---------+-------------------------
  1 | Ann  | ann@gmail.com  |       0 | 2020-09-27 22:11:20.333
  2 | Anne | anne@gmail.com |       0 | 2020-09-28 18:37:23.064
  3 | John | john@gmail.com |       0 | 2020-09-28 18:48:23.699
(3 rows)
```
Then need to change response to user to be more descriptive and user[0] to return first user in array then also add catch err:

```
app.post('/register',(req,res)=>{
	const {email, name, password} =req.body;
	bcrypt.hash(password, null, null, function(err, hash) {
    	console.log(hash);
	});

	db('users')
		.returning('*')
		.insert ({
		email: email,
		name: name,
		joined: new Date()
		})
		.then (user => {
			res.json(user[0]);
		})	
		.catch(err => res.status(400).json(err))
})
```
If try to reregister same user from Postman get an error: 
```
{
    "length": 273,
    "name": "error",
    "severity": "ERROR",
    "code": "23505",
    "detail": "Key (email)=(john@gmail.com) already exists.",
    "schema": "public",
    "table": "users",
    "constraint": "users_email_key",
    "file": "d:\\pginstaller.auto\\postgres.windows-x64\\src\\backend\\access\\nbtree\\nbtinsert.c",
    "line": "535",
    "routine": "_bt_check_unique"
}
```
Despite getting 400 error we are returning info about database. This gives too much inormation for hackers. Just change to "unable to register".
```
.catch(err => res.status(400).json("unable to join"))
```

Can now register from front-end:
```
smartbrain=# SELECT * FROM users;
 id | name |      email       | entries |         joined
----+------+------------------+---------+-------------------------
  1 | Ann  | ann@gmail.com    |       0 | 2020-09-27 22:11:20.333
  2 | Anne | anne@gmail.com   |       0 | 2020-09-28 18:37:23.064
  3 | John | john@gmail.com   |       0 | 2020-09-28 18:48:23.699
  8 | Amy  | theamy@gmail.com |       0 | 2020-09-28 19:36:15.137
(4 rows)
```

