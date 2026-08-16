# Express main idea:


# Step by Step:
## 001- initiate npm framework at same file directory
npm init --y  

## 002- install express nodejs modules
npm install express --save 

## 003- after creating server.js file 
node server.js   to run the express app

## 004- sample file code

""" const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello, World! ya haitham ya gamed v2');
});

app.listen(3000, () => {
    console.log(`Server is running on port 3000 ya haitham ya gamed`);
});
"""

this sample code for express backend server file.

## 005- install nodemon 
npm i -g nodemon  
 nodemon used to automaticaly update server with latest update in file codes
then use comand : nodemon server.js to run the server with nodemon instead of node

 ## 005- update package.json file use/create scripts to run nodemon server:
 ### sample:
 "scripts": {
    "dev": "nodemon server.js",
    "7lwteha": "nodemon server.js"
  },  dh kda by3ml comand asmo 7lwateha lma t3mlo run hwa mn nfso by run nodemon server.js

   npm run 7lwteha 

   ## 006 install dotenv to use env variable 
   npm install dotenv

   ## 007 install morgan to use logger 
   npm i morgan 

   ## 008 async handler package:
   npm install --save express-async-handler
   intsall this package to wrap the async - await function to handle error without using try/catch or then/catch

   ### example: 
   asyncHandler(async (req, res) => {

        const category = await CategoryModel.create({ name: req.body.name, slug: req.body.slug });
        res.status(201).json({
            status: 'success',
            data: category,
        });
    })

## 009- Error Handling ways:

### 01- .then().chatch(err)
### 02- try() / catch(err)
### 03- asynchandler(async(req, res)) => {}