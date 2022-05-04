# PROJECT_4
MEAN_STACK_IMPLEMENTATION
## Step 1: Install NodeJs
- I used `sudo apt update` and `sudo apt upgrade` to update and upgrade my server packages to the latest version 

![Screen Shot 2022-05-03 at 6 02 20 PM](https://user-images.githubusercontent.com/101080172/166585077-721c58f0-25f3-44ae-a357-37ac12c86600.png)
![Screen Shot 2022-05-03 at 6 02 39 PM](https://user-images.githubusercontent.com/101080172/166585117-eb0c1503-aab5-470d-8151-8189b4e00809.png)

        
  - Then added certificates by using the following commands
  ````
  sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
````
  
  ![Screen Shot 2022-05-03 at 6 02 55 PM](https://user-images.githubusercontent.com/101080172/166604810-d0c866e1-7956-40d3-bd69-80849ba839ed.png)
 
- I Installed NodeJS using `sudo apt install -y nodejs`

  ![Screen Shot 2022-05-03 at 6 03 08 PM](https://user-images.githubusercontent.com/101080172/166604480-07febe24-b940-4f27-8972-0f4f60786c93.png)

## Step 2: Install MongoDB

-I imported the keys for MangoDB  and install MongoDB by using the following commands
````
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

sudo apt install -y mongodb
````
![Screen Shot 2022-05-03 at 6 03 36 PM](https://user-images.githubusercontent.com/101080172/166606172-6ec7db30-ccf7-48ef-9828-8006fde5513c.png)

- I starterd the MangoDB dabase and installed Node package Manager through the following commands
````
sudo service mongodb start
sudo systemctl status mongodb
sudo apt install -y npm
````

![Screen Shot 2022-05-03 at 6 03 59 PM](https://user-images.githubusercontent.com/101080172/166606418-58bd0ec4-8217-4db0-9f78-4ae6b0607cad.png)

-Installed body-parser package `sudo npm install body-parser`
![Screen Shot 2022-05-03 at 8 30 34 PM](https://user-images.githubusercontent.com/101080172/166608316-c9ff5c0b-2685-4c33-973a-6adbcd83bd6f.png)

- Created a folder named ‘Books’ and Initialized npm project in the folder 

````
mkdir Books && cd Books

npm init

````

![Screen Shot 2022-05-03 at 6 05 20 PM](https://user-images.githubusercontent.com/101080172/166608036-d75ae9c9-454a-4f10-9a5b-4fecce6d4934.png)


-Added a file to the folder named server.js and added the following scrip to it

````
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

````

![Screen Shot 2022-05-03 at 6 05 45 PM](https://user-images.githubusercontent.com/101080172/166608926-16d4ed9b-6aa6-42cf-8e83-8224dc7d60a2.png)


## Step 4: Install Express and set up routes to the server

-  We install express and mongoose packages using `sudo npm install express mongoose`

![Screen Shot 2022-05-03 at 6 05 45 PM](https://user-images.githubusercontent.com/101080172/166608926-16d4ed9b-6aa6-42cf-8e83-8224dc7d60a2.png)

-In book folder create a folder named apps and inside of Apps, we created a file name routes.js

`mkdir apps && cd apps && touch rountes.js`

- the script inside of routes

````
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
````
- Created a folder named models in the apps and created a file name book.js
- inside of books I added the following script
````var mongoose = require('mongoose');
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

````


![Screen Shot 2022-05-03 at 6 06 25 PM](https://user-images.githubusercontent.com/101080172/166611182-2cdc58a8-4063-49af-b670-bf1ff3213f4b.png)



## Step 4 – Access the routes with AngularJS

- Create a folder named public in the Book folder and added a file called script.js, see picture for script added to script.js
````
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
````

![Screen Shot 2022-05-03 at 6 06 25 PM](https://user-images.githubusercontent.com/101080172/166611182-2cdc58a8-4063-49af-b670-bf1ff3213f4b.png)

![image](https://user-images.githubusercontent.com/101080172/166612425-922d2ab5-0647-4fb8-b75c-312988620b4f.png)


- In the public folder we created a file named index.html;

- in index.html , we added the following script
 ````
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
````
![Screen Shot 2022-05-03 at 6 07 10 PM](https://user-images.githubusercontent.com/101080172/166616313-7f287da0-5d3a-4db0-98dc-1838a5aa7888.png)

- I started the he server using `node server.js`

 ![image](https://user-images.githubusercontent.com/101080172/166616815-a5d1eeb3-bd9f-48c2-86f2-644b70e31cd2.png)
 
 
- when I used the IP followed by :3300 , this what we get

![image](https://user-images.githubusercontent.com/101080172/166616975-a01517cd-daac-4777-8552-27931be252db.png)


 
