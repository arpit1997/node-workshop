# Add some data

Great [demo](http://cwbuecheler.com/web/tutorials/2013/node-express-mongo/) that adds the next layer of awesome to this workshop. Adding a Mongo DB to the project to make a simple i/o UX.

> My favorite thing about MongoDB is that it uses JSON for its structure, which means it was instantly familiar for me. If you're not familiar with JSON, you'll need to do some reading, as I'm afraid that's outside the scope of this tutorial.

> Let's add a record to our collection. For the purposes of this tutorial, we're just going to have a simple database of usernames and email addresses. Our data format will thus look like this:

```javascript
{
  "_id" : 1234,
  "username" : "cwbuecheler",
  "email" : "cwbuecheler@nospam.com"
  "password": "375hdgdj45#"
}
```
First of all install mongodb from [here](https://docs.mongodb.com/manual/installation/)
Once installation is completed, To import mongodb add this to your `app.js`
```javascript
var MongoClient = require('mongodb').MongoClient
	, assert = require('assert');
```
By default mongodb runs on port number `27017`. So we will now connect to mongodb database by adding this to our `app.js` file
```
var url = 'mongodb://localhost:27017/myproject';
MongoClient.connect(url, function(err, db) {
  assert.equal(null, err);
  console.log("Connected successfully to server");
  createCapped(db, function(){
  		db.close();
  		console.log("closed");
  });
  });
```
Now its time to add data to database. Modify `app.js` 
```
MongoClient.connect(url, function(err, db) {
  assert.equal(null, err);
  console.log("Connected successfully to server");
  createCapped(db, function(){
  		db.collection('mycollections').insert({username:198, email:"arpit@gmal.com", password:"yash123"})
  		db.close();
  		console.log("closed");
  });
  });

var createCapped = function(db, callback){
	db.createCollection('mycollections', {"capped":true, "size":100000, "max":5000},
		{
	      'validator': { '$and':
	         [
	            { 'username': { '$type': "string" } },
	            { 'email': { '$regex': /@gmail\.com$/ } },
	            {'password': { '$regex': /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/ }}
	         ]
	    	}
	    	
		},
		{
			validationLevel:'moderate'	
		},
		function(err, results){
			console.log(err);
			assert.equal(null, err);
			console.log("created successfully");
			console.log(db.collection('mycollection'));
			callback();
		}
	)
}
```
Anyone out there willing to help put the rest of this together, I would love a Pull Request!
