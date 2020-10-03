## Sign In ##

### Registering using transactions to update login and user db's ###
 
Transactions - are code blocks that we add to make sure when doing multiple operations when one fails then they all fail. 
So if you can add to users but not login then they will both fail.

Get data from bcrypt and add transaction that insert hash and email into login table (and users data to users table). This returns email that is used as loginEmail. This then adds data to users table using prior code but change return db to return trx ('users') and insert data. ***** not sure what return and returning does *******: 

Also need to add 
```
.then(trx.commit)
.catch(trx.rollback)
```

Commit gets data (trx) sent to database.
Rollback rolls any changes back if anything goes wrong.

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
To get email fixed do (already added to code above): 
```
email: loginEmail[0],
```
If do John1@gmail.com POST in Postman get it as follows in response:
```
{
    "id": 16,
    "name": "John",
    "email": "john1@gmail.com",
    "entries": "0",
    "joined": "2020-10-03T07:19:32.074Z"
}
```
### Sign In ###
Delete old stuff from /signin post. Then add the below.
This gets email and hash from login db where the value matches what's in the body in Postman and logs it in console.
```
app.post('/signin',(req,res)=>{

	db.select('email', 'hash').from('login')
		.where('email', '=', req.body.email)
		.then(data => {
			console.log(data);
		})
})

```
Output when do POST localhost: 300/signin in Postman:
```
[
  {
    email: 'john@gmail.com',
    hash: '$2a$10$qJMmQrL9i3AMn9fySSu3ZuSX0eVtwcjkNxCIsizOxJXOAB45zOUSe'
  },
  {
    email: 'john1@gmail.com',
    hash: '$2a$10$BdIDWY2GzAlXhgYzCkyXE.l4AYtvofBFA/o6RZoNCzOTG1DO/cqmK'
  }
]
```
#### bcrypt compareSync and respond with json ####
Then set isValid to compare body password with hash value using bcrypt. If true select all columns from users where email matches whats in body. Then respond with user[0] and add error catches:
```
app.post('/signin',(req,res)=>{
	
	db.select('email', 'hash').from('login')
		.where('email', '=', req.body.email)
		.then(data => {
			
			const isValid=bcrypt.compareSync(req.body.password, data[0].hash);
			console.log(isValid);
			if(isValid){
				return db.select('*').from ('users')
					.where('email', '=', req.body.email)
					.then(user => {
						res.json(user[0])
					})
					.catch(err => res.status(400).json('unable to get user'))
			} else {
			res.status(400).json('wrong dredentials')
			}
		})
		.catch(err => res.status(400).json('wrong credentials'))
})
```
Output when do POST localhost: 300/signin in Postman:
```
{
    "id": 16,
    "name": "John",
    "email": "john1@gmail.com",
    "entries": "0",
    "joined": "2020-10-03T07:19:32.074Z"
}
```
