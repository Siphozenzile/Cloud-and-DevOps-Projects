#  Book Register Web form using MEAN stack in AWS.

Below are all the steps i took to complete the MEAN stack implementation project.

A MEAN Stack is a combination of following components:

- MongoDB (Document database) – Stores and allows to retrieve data.
  
- Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
  
- Angular (Front-end application framework) – Handles Client and Server Requests.
  
- Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user

## Requirements :

1. AWS account
2. Virtual Server with Ubuntu OS
3. Basic Linux Understanding

## Challenges :

Mean Stack:

### Challenge 1: I couldn't start node server.js

/home/ubuntu/Books/node_modules/mongodb/lib/admin.js:62
            session: options?.session,
                             ^

SyntaxError: Unexpected token '.'
    at wrapSafe (internal/modules/cjs/loader.js:915:16)
    at Module._compile (internal/modules/cjs/loader.js:963:27)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1027:10)
    at Module.load (internal/modules/cjs/loader.js:863:32)
    at Function.Module._load (internal/modules/cjs/loader.js:708:14)
    at Module.require (internal/modules/cjs/loader.js:887:19)
    at require (internal/modules/cjs/helpers.js:74:18)
    at Object.<anonymous> (/home/ubuntu/Books/node_modules/mongodb/lib/index.js:6:17)
    at Module._compile (internal/modules/cjs/loader.js:999:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1027

The solution is to upgrade node.js.

Here is how (Ubuntu 14), see https://askubuntu.com/questions/426750/how-can-i-update-my-nodejs-to-the-latest-version

sudo npm cache clean -f

sudo npm install -g n

sudo n stable

sudo ln -sf /usr/local/n/versions/node/<VERSION>/bin/node /usr/bin/nodejs


### Challenge 2: Package has no installation for database (Mongodb)

This is step 2, where we have to install our database MOngoDB.

Package has no installation (for mongodb)

Folow the steps in this website : https://www.cherryservers.com/blog/install-mongodb-ubuntu-22-04


## Step 1 : Launch a new EC2 instance and connect using EC2 instace connect (CLI)

Launch a new EC2 machine, use Ubuntu OS and connect to EC2 using the CLI (EC2 instance connect) and then press connect.

![Screenshot (77)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/589b4bd9-2f7c-4fbd-90e4-7684143741f2)

![Screenshot (78)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/04d5107e-6fa2-49bf-9c81-e47add083626)

## Step 2: Install NodeJs 

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used to set up the Express routes and AngularJS controllers.

Update ubuntu :

```
sudo apt update
```

Upgrade ubuntu:
```
sudo apt upgrade
```

Add certificates:
```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```
Install NodeJS:
```
sudo apt install -y nodejs
```
## Step 2: Install MongoDB :

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

mages/WebConsole.gif
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" |
sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```
Install MongoDB :
```
sudo apt install -y mongodb
```

Start The server :
```
sudo service mongodb start
```
In the Books directory, Initialize npm project :
```
npm init
```
Add a file to it named server.js :

```
Add a file to it named server.js
```
Copy and paste the web server code below into the server.js file :
```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```
## Step 3: Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.
```
sudo npm install express mongoose
```

In ‘Books’ folder, create a folder named apps :
```
mkdir apps && cd apps
```

Create a file named routes.js :
```
vi routes.js
```

Copy and paste the code below into routes.js :
```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

In the ‘apps’ folder, create a folder named models :

```
mkdir models && cd models
```

Create a file named book.js :
```
vi book.js
```

Copy and paste the code below into ‘book.js’ :
```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```

![Screenshot (57)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/36423448-339f-4ec6-aecf-6fb75e6f0ca8)

## Step 4 : Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to ‘Books’ :

```
cd ../..
```

Create a folder named public :

```
mkdir public && cd public
```

Add a file named script.js :

```
vi script.js
```

Copy and paste the Code below (controller configuration defined) into the script.js file :
```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```

In public folder, create a file named index.html; :

```
vi index.html
```

Copy and paste the code below into index.html file :

```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>

```

Change the directory back up to Books :

```
cd ..
```
Start the server by running this command: 
```
node server.js
```

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally :

```
curl -s http://localhost:3300
```

It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

![Screenshot (74)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/8fe37f6b-6fe4-457b-ab23-ab6cd15db2e6)

Now you can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

This is how your Web Book Register Application will look like in browser:

![Screenshot (76)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/ad5a4535-29e5-4248-ad0a-c3a3cc1a9c36)




















