## Sign In ##
 
Transactions - are code blocks that we add to make sure when doing multiple operations when one fails then they all fail. 
So if you can add to users but not login then they will both fail.

Get data from bcrypt: 

```
app.post('/register',(req,res)=>{
	const {email, name, password} =req.body;

	const hash = bcrypt.hashSync(password);

	db.transaction(trx=> {
		trx.insert({
			hash: hash,
			email: email
		})
		.into ('login')
		.returning('email')
		.then (loginEmail=>{
			return trx('users')
			.returning('*')
			.insert ({
			email: loginEmail[0],
			name: name,
			joined: new Date()
			})
			.then (user => {
				res.json(user[0]);
			})	

		})
		.then(trx.commit)
		.catch(trx.rollback)
	})
	.catch(err => res.status(400).json("unable to join"))
})

```


When do POST localhost:3000/register Postman this returns:
```
{
    "id": 14,
    "name": "John",
    "email": "{\"john@gmail.com\"}",
    "entries": "0",
    "joined": "2020-10-03T06:25:01.990Z"
}
```
This returns: 
```
smartbrain=# SELECT * FROM login;
 id |                             hash                             |     email
----+--------------------------------------------------------------+----------------
  7 | $2a$10$qJMmQrL9i3AMn9fySSu3ZuSX0eVtwcjkNxCIsizOxJXOAB45zOUSe | john@gmail.com
(1 row)
```
