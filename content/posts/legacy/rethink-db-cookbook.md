---
title: "Rethink-DB Cookbook"
date: 2018-11-05T21:15:35-07:00
draft: false
---

(Note: this is from an old blog archieve dating 2018/11/05. Some things with Rethink have very likely changed)
RethinkDB is a JSON based, non-relational database that provides a promise oriented, Node JS backend. It integrates seamlessly with JSON type data  and is a production ready option for Node infrastructures.

Pre-reqs: Docker, Node, NPM

This post will serve as a brief overview of RethinkDB and hopefully give you a taste of how it works and why a JSON based database might be beneficial for you and your product. You should have some knowledge of docker for this tutorial, but it's not required. However, knowledge of Node and JavaScript will be necessary. 

# Run the offical Docker Image
You can pull and run the [official](https://hub.docker.com/_/rethinkdb/) rethink docker image to start the database locally. Simply give it a name and you're on your way!
```bash
docker run -d -P --name <your container name> rethinkdb
```
To check the port mappings in docker, simply run
```bash
docker port <your container name>
```
This will show you something like this:
```
28015/tcp -> 0.0.0.0:32769
29015/tcp -> 0.0.0.0:32768
8080/tcp -> 0.0.0.0:32770
```
Each of the local port mappings appear on the right and the docker container exposed ports are on the left.

So, if you wanted to access the containers 8080 port, we would navigate to `localhost:32770`. We can see this from the example as `8080/tcp -> 0.0.0.0:32770`. 

Alternatively, you can install rethinkdb for your specific machine and run it locally. This can be found on the [RethinkDB install page](https://rethinkdb.com/docs/install/). Using the docker container & image is a nice, light weight, modular way to run rethink, similar to how a production microservice architecture might be configured. I also like being able to control the exact environment that my rethink database is running in, it's ports, and other fun docker quality of life things!

# Using the RethinkDB admin pannel

If using the docker container, navigate to the Admin Panel by going to `localhost:32770` in a browser. From our previous example, we can see that this local port is mapped to the docker container port 8080 (which is the web admin panel). If you're running rethink on your machine locally, you should be able to simply navigate to `localhost:8080`.

In the admin pannel, you create new databases, explor data, see logs, track performance, and see what connections are running. Lets create a database with a few tables.

In the top navigation bar, go to the "Data Explorer" and enter the following:

```javascript
r.dbCreate('ships')
r.db('ships').tableCreate('battle_ships')
r.db('ships').tableCreate('cruisers')
```
These raw rethink queries create and build our initial database. This can also be acomplished from "Tables" in the top navigation bar or right in your node app! However, the "Data Explorer" is an essential tool for viewing, manuipulating, and creating data. This is a great [link](https://rethinkdb.com/docs/reql-data-exploration/) for useful Data Explorer queries.  

# Install Rethink javascript drivers via NPM
In order to use the rethink drivers in our Node app, we need to install them via NPM. From the command line:

```javascript
npm install rethinkdb
```

The `node_modules` folder will now contain the necessary rethink drivers for accessing our rethink instance. To access the rethink drivers from your Node app, require the drivers:

```javascript
const r = require('rethinkdb');
```


# Open a connection to the Rethink instance

```javascript
let connection = null;

// This could also be declared from a .env file
let config = {
  host: 'localhost',
  port: '32769'
}

r.connect(config, function(err, conn) {
  if (err) throw err;
  connection = conn;
});
```

Now, the connection variable will hold the raw data necessary to connect to the rethink instance. Make special note of what port you specify. This should be the port that maps to 28015 in the docker container.

For this local instance of the rethinkdb, we won't worry too much about dynamic ports or not exposing the ports to the public in production. [Here](https://medium.com/@brucelim/creating-a-rest-api-with-rethinkdb-nodejs-express-81ed12f01e59) is a good article about one way you can create production ready ports and configurations.

This step can be quite complicated. You can do a number of things per your needs, including placing this step into some middleware to connect automatically, check the configuration of your database, reconfigure settings if something is wrong, or validate authorization. Check out [this repository](https://github.com/rethinkdb/rethinkdb-example-nodejs) from the rethink people for more complex operations around connecting. 

# Basic Crud Operations

### Insert Data

```javascript
// Inserts 2 battleships
r.db('ships').table('battle_ships').insert([
  {
    'name': 'Arizona',
    'size': 22,
    'guns': [
      'railgun',
      'off_shore_missles'
  ]
  }, {
    'name': 'Iowa',
    'size': 34,
    'guns': [
      'light_machine'
  ]
}]).run(connection, function (err, res) {
  if (err) throw err;
  console.log(JSON.stringify(res));
})
```

We can see that we are inserting raw JSON objects! Awesome! Now, from the Data explorer, if we query the `battle_ships` table:

```javascript
r.db('ships').table('battle_ships')
```
We will see the following JSON has been entered into the database:

```javascript
{
    "guns": [
        "railgun" ,
        "off_shore_missles"
    ] ,
    "id": "35502dbd-0354-4ca8-bef5-06825ab8df26" ,
    "name": "Arizona" ,
    "size": 22
}
{
    "guns": [
        "light_machine"
    ] ,
    "id": "b960127b-994f-44b7-88f5-f7463fc90dae" ,
    "name": "Iowa" ,
    "size": 34
}
```

### Getting data
```javascript
// Get all battle ships
r.db('ships').table('battle_ships')
  .run(connection, function(err, cursor) {
    if (err) throw err;
    cursor.toArray(function(err, res) {
        if (err) throw err;
        console.log(JSON.stringify(res));
    });
});
```

In this example, we are getting all the ships in the battle_ships table. Most rethink queries of this size will return a cursor by default, so to get the raw results, we must make it an array with the `.toArray` method. The callback will contain the results that can than be parsed further. 

```javascript
// Get specific battleship
r.db('ships').table('battle_ships')
  .get('35502dbd-0354-4ca8-bef5-06825ab8df26')
  .run(connection, function(err, res) {
      if (err) throw err;
      console.log(JSON.stringify(res));
  });
```
This gets a single ship from the battle_ships table based on the primary key. The primary key is the ID automatically assigned to the inserted JSON. The results we get back in the callback function is the JSON object of the provided key. 

### Update JSON objects

```javascript
// Update the length of The Texas battle ship
r.db('ships').table('cruisers').filter(r.row("name").eq("Texas"))
  .update({
    "length": 33
  }).run(connection, function(err, res) {
      if (err) throw err;
      console.log(JSON.stringify(res));
  });
```

Here, we update a ship's length by providing an updated JSON object. Note that we don't need to provide all fields of the object in order for it to be updated. Once we `run` the query, the returned result will be what was updated in the database. This snippet also introduces the `.filter` rethink method. This can be used to pull specific records based on a number of conditions. Finding json objects this way is very powerful and can be chained with other queries. Almost anything you can do with SQL or Mongo, you can do with rethink queries. Check out [this awesome page](https://rethinkdb.com/docs/introduction-to-reql/) for some really useful queries. 

### Delete JSON data
```javascript
// Remove the Iowa battle ship
r.db('ships').table('battle_ships')
  .filter(r.row('name').eq('Iowa'))
  .delete()
  .run(connection, function(err, res) {
        if (err) throw err;
        console.log(JSON.stringify(res));
    });
```
Here, we again use the `.filter` method to find a document in the database. Then, we delete it using the `.delete()` rethink method. After running this query, the JSON will be removed from the database. 

# Conclusion
I hope that this little dive into RethinkDB has been interesting and has you curious about JSON based databases. Being able to store raw JSON in a NoSQL database is extremely powerful and fits well with JavaScript based architectures.

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
