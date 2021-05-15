<h1> In this project, we would be implementing a web solution based on MEAN stack in AWS Cloud.</h1>

![host-mean-app-on-aws-img](https://user-images.githubusercontent.com/10243139/118354590-a20bbc80-b563-11eb-9aa1-4e50fefb5c5e.jpg)

<h2>MEAN Web stack consists of following components:</h2>

- [ ]	MongoDB: (Document database) - Stores and allows to retrieve data.
- [ ]	Express: (Back-end application framework) - Makes requests to Database for Reads and Writes.
- [ ]	Angular: (Front-end application framework) - Handles Client and Server Requests
- [ ]	Node.js: (JavaScript runtime environment) - Accepts requests and displays results to end user

<h2>Step 1: Install NodeJs</h2>

<h3>This would be used in this project to set up the Express routes and AngularJS controllers.</h3>

<h4>Install Node.js on the server by running the following configurations:</h4>

- [ ] Update Ubuntu:
- [x] sudo apt update

- [ ] Upgrade Ubuntu:
- [x] sudo apt upgrade

- [ ] Install NodeJS:
- [x] sudo apt install -y nodejs


<h2>Step 2: Install MongoDB</h2>

<h3>This is used to store data in flexible, JSON-like documents</h3>

<h4>Install MongoDB and verify it is up and running</h4>
	
- [ ] Run these two commands:
- [x] sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
- [x] echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

![2 a i](https://user-images.githubusercontent.com/10243139/118356743-a25d8500-b56e-11eb-8311-4e0ea59aab57.jpg)
	
- [ ] Install MongoDB by running the command below:
- [x] sudo apt install -y mongodb

- [ ] Start the server with the command below:
- [x] sudo service mongodb start

- [ ] Verify the server is up and running:
- [x] sudo systemctl status mongodb

![2 a iv](https://user-images.githubusercontent.com/10243139/118356788-d769d780-b56e-11eb-8fb8-1649dce6d426.jpg)

<h4>Install other dependencies</h4>

- [ ] Install the Node Package Manager:
- [x] sudo apt install -y npm
	
- [ ] Install body-parser package which is needed to help us process JSON files passed in requests to the server:
- [x] sudo npm install body-parser

- [ ] Now lets create a folder named ‘Books’:
- [x] mkdir Books

- [ ] Lets switch into the Books folder:
- [x] cd Books/

- [ ] In the Books directory, Initialize npm project:
- [x] npm init
- [ ] You would need to press enter repeatedly till it brings back the terminal prompt as shown in the image below

![2 b vi](https://user-images.githubusercontent.com/10243139/118356802-e781b700-b56e-11eb-82d9-9a6292d56474.jpg)

- [ ] Add a file to the current directory named server.js:
- [x] nano server.js

- [ ] Copy the following code in the server.js file:

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
      
<h2>Step 3: Install Express and set up routes to the server</h2>

<h3>Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.</h3>

- [ ] We need to install Mongoose to establish a schema for the database to store data of our book register:
- [x] sudo npm install express mongoose

- [ ] In Books folder, create a folder named apps and switch into the apps folder:
- [x] mkdir apps && cd apps

- [ ] Lets create a file named routes.js and open the file:
- [x] nano routes.js

- [ ] Copy the following into the routes.js file: 

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
      
  
- [ ] In the apps folder, create a folder named models and switch into the folder:
- [x] mkdir models && cd models

- [ ] Create a file named book.js:
- [x] nano book.js

- [ ] Copy the following code in just created book.js file:

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
      
<h2>Step 4 - Access the routes with AngularJS</h2>

<h3>AngularJS provides a web framework for creating dynamic views in our web applications. For this project, we would use AngularJS to connect our web page with Express and perform actions on our book register.</h3>

- [ ] Lets change the directory back to ‘Books’:
- [x] cd ../..

- [ ] Create a folder named public and switch into the public folder:
- [x] mkdir public && cd public

- [ ] Add a file named script.js:
- [x] nano script.js

- [ ] Copy and paste the Code below (controller configuration defined) into the script.js file:

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
      
      
- [ ] In public folder, create a file named index.html:
- [x] nano index.html

- [ ] Copy and paste the code below into index.html file:

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
      
      
- [ ]  Change the directory back up to Books:
- [x] cd ..

- [ ] Start the server by running this command:
- [ ] node server.js      

![4 viii](https://user-images.githubusercontent.com/10243139/118357336-8e675280-b571-11eb-8622-ec204d391bff.jpg)

- [ ] You need to open TCP port 3300 in your AWS Web Console for your EC2 Instance

![4 ix](https://user-images.githubusercontent.com/10243139/118357352-a343e600-b571-11eb-997b-9ed29b22d506.png)

- [ ] Now you can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

![4 x](https://user-images.githubusercontent.com/10243139/118357367-b5258900-b571-11eb-8a0d-085405972850.jpg)

- [ ] This is what the backend looks like from the console when you add data from the browser

![4 xi](https://user-images.githubusercontent.com/10243139/118357386-ca9ab300-b571-11eb-9256-87a93f795e85.jpg)

- [ ] This is what it looks like when you refresh the browser

![4 xii](https://user-images.githubusercontent.com/10243139/118357401-dd14ec80-b571-11eb-9b48-0b20117a7da4.jpg)

Congratulations!

