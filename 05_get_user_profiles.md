Add to profile get function: 

```
app.get('/profile/:id', (req, res) => {
	const {id} = req.params;
	let found=false;
	// database.users.forEach(user => {
	// 	if(user.id === id) {
	// 		found=true;
	// 		return res.json(user);
	// 	} 
	// })
	*** db.select('*').from('users').then(user => {
		console.log(user);
	}) ***
	
	if(!found){
		res.status(404).json('no such user');
	}
})

```
Do get request in Postman: 

```
localhost:3000/profile/1
```
This returns all users in console: 
```
[
  {
    id: 1,
    name: 'Ann',
    email: 'ann@gmail.com',
    entries: '0',
    joined: 2020-09-27T18:11:20.333Z
  },
  {
    id: 2,
    name: 'Anne',
    email: 'anne@gmail.com',
    entries: '0',
    joined: 2020-09-28T14:37:23.064Z
  },
  {
    id: 3,
    name: 'John',
    email: 'john@gmail.com',
    entries: '0',
    joined: 2020-09-28T14:48:23.699Z
  },
  {
    id: 8,
    name: 'Amy',
    email: 'theamy@gmail.com',
    entries: '0',
    joined: 2020-09-28T15:36:15.137Z
  }
]
```
Go to knex where section:
http://knexjs.org/#Builder-where
 then add the following based on info there:
 
 ```
 db.select('*').from('users').where ({
		id: id
	})

	.then(user => {
		console.log(user);
	})
  ```
 This should give when do Postan man get as used before (i.e. user 1):
  ```
  [
  {
    id: 1,
    name: 'Ann',
    email: 'ann@gmail.com',
    entries: '0',
    joined: 2020-09-27T18:11:20.333Z
  }
]
 To just grab user without Array brackets use [0] after user, also change console.log resp.json. Also use ES6 on id as id=id: 
  ```
app.get('/profile/:id', (req, res) => {
	const {id} = req.params;
	// let found=false;

	db.select('*').from('users').where (
		{id}
	)

	.then(user => {
		res.json(user[0]);
	})

	// if(!found){
	// 	res.status(404).json('no such user');
	// }
});

  ```
  Add in check to see if user exists as found is not working now. 
  
```
app.get('/profile/:id', (req, res) => {
	const {id} = req.params;
	// let found=false;

	db.select('*').from('users').where (
		{id}
	)

	.then(user => {
		if(user.length) {
			res.json(user[0])
		} else {
			res.status(400).json('not found')
		}
	})
	.catch(err => res.status(400).json('error getting user'))
	
})
```
