#SIMPLE TO-DO APPLICATION ON MERN WEB STACK

MERN Web stack consists of following components:

MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

ExpressJS: A server side Web Application framework for Node.js.

ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

i started the project by updating and upgrading ubuntu by running the following commands. 

       sudo apt update

then 

       sudo apt upgrade

to get the location of Node.js software from Ubuntu repositories. i had to run the following comand 

       curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

i then installed Node.js using the following command. 
  
       sudo apt-get install -y nodejs

<img width="1074" alt="Screenshot 2022-06-06 at 18 02 17" src="https://user-images.githubusercontent.com/103360590/172209677-38b32e53-93b3-4851-a787-1969248715fd.png">

 The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.
 
 after the installations i used the following commands to verify the versions. 
 
      node -v
  then 
      
      npm -v 

   Application Code Setup
 
i then Created a new directory for my To-Do project by running the following command

       mkdir Todo
       
i then ran the command below to verify that the Todo directory is created 

       ls

TIP: In order to see some more useful information about files and directories, you can use following combination of keys 

       ls -lih – 

it will show you different properties and size in human readable format. You can learn more about different useful keys for ls command with 

        ls --help.
        
i then change the current directory to the newly created one using the following command

        cd Todo

Next, you will use the command 
        
        npm init 
        
to initialise the project, so that a new file named package.json will be created. This file will normally contain information about the application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes.

<img width="1074" alt="Screenshot 2022-06-06 at 18 35 11" src="https://user-images.githubusercontent.com/103360590/172214632-3662c765-dbae-4f75-880b-43215ab1b616.png">
 
 i then ran 
 
        ls 
        
 to confirm that you have package.json file created.
 
       INSTALL EXPRESSJS

Remember that Express is a framework for Node.js, therefore a lot of things developers would have programmed is already taken care of out of the box. Therefore it simplifies development, and abstracts a lot of low level details. For example, Express helps to define routes of your application based on HTTP methods and URLs.

To use express, i installed it using npm

         npm install express
         
i then created a file index.js with the command below

         touch index.js

i then ran
       
       ls 
       
to confirm that my index.js file is successfully created

i then Installed the dotenv module next by running the command 

       npm install dotenv

i then Opened the index.js file with the command below

      vim index.js
      
and typed the code bellow and save and exit using 
      :w and :qa

         const express = require('express');
         require('dotenv').config();

         const app = express();

         const port = process.env.PORT || 5000;

         app.use((req, res, next) => {
         res.header("Access-Control-Allow-Origin", "\*");
         res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-          Type, Accept");
         next();
         });

         app.use((req, res, next) => {
         res.send('Welcome to Express');
         });

         app.listen(port, () => {
         console.log(`Server running on port ${port}`)
         });

to check if the server works, i ran the following command

         node index.js
         
i then opened this port in EC2 Security Groups. inbound rule port 5000. then i accessed the server thru the following

        http://<PublicIP-or-PublicDNS>:5000

Routes

There are three actions that our To-Do application needs to be able to do:

1 Create a new task

2 Display list of all tasks

3 Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, i created routes that will define various endpoints that the To-do app will depend on. i created a folder route using the following command

       mkdir routes

then i Changed the directory to routes folder using

           cd routes

i then created a file api.js with the command below

           touch api.js

then i Opened the file with the command below

           vim api.js
           
then i wrote the following code in the file 

          const express = require ('express');
          const router = express.Router();

          router.get('/todos', (req, res, next) => {

          });

          router.post('/todos', (req, res, next) => {

          });

          router.delete('/todos/:id', (req, res, next) => {

          })

          module.exports = router;
          
 MODELS
 
 since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.

A model is at the heart of JavaScript based applications, and it is what makes it interactive.

We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document. (Seems like a lot of information, but not to worry, everything will become clear to you over time. I promise!!!)

In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties

To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

i Change directory back to Todo folder with cd .. and install Mongoose
 
             npm install mongoose
              
i then Created a new folder  models
 
             mkdir models
             
the next step was to Change directory into the newly created ‘models’ folder with
 
             cd models
             
Inside the models folder, i created a file and named it todo.js

             touch todo.js
             
all three commands above can be executed consecutively with the following command

           mkdir models && cd models && touch todo.js
           
i then opened the file created with vim todo.js then paste the code below in the file:

          const mongoose = require('mongoose');
          const Schema = mongoose.Schema;

          //create schema for todo
          const TodoSchema = new Schema({
          action: {
          type: String,
          required: [true, 'The todo text field is required']
          }
          })

          //create model for todo
          const Todo = mongoose.model('todo', TodoSchema);

          module.exports = Todo;
          
i had to update the routes from the file api.js in ‘routes’ directory to make use of the new model.

In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit

          const express = require ('express');
          const router = express.Router();
          const Todo = require('../models/todo');

          router.get('/todos', (req, res, next) => {

          //this will return all the data, exposing only the id and action field to the           client
          Todo.find({}, 'action')
          .then(data => res.json(data))
          .catch(next)
          });

          router.post('/todos', (req, res, next) => {
          if(req.body.action){
          Todo.create(req.body)
          .then(data => res.json(data))
          .catch(next)
          }else {
          res.json({           
          error: "The input field is empty"
          })
          }
          });

          router.delete('/todos/:id', (req, res, next) => {
          Todo.findOneAndDelete({"_id": req.params.id})
          .then(data => res.json(data))
          .catch(next)
          })

          module.exports = router;
             
MONGODB DATABASE

i needed a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Sign up here. Follow the sign up process, select AWS as the cloud provider, and choose a region near you.

<img width="953" alt="Screenshot 2022-06-06 at 23 37 50" src="https://user-images.githubusercontent.com/103360590/172261016-8bc49be1-a884-481d-8a67-66a946fc5820.png">

Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing)

IMPORTANT NOTE
In the image below, make sure you change the time of deleting the entry from 6 Hours to 1 Week

<img width="953" alt="Screenshot 2022-06-06 at 23 38 15" src="https://user-images.githubusercontent.com/103360590/172261066-044d9222-149c-4ba1-a07b-f7a38ba9d00d.png">

Create a MongoDB database and collection inside mLab

<img width="953" alt="Screenshot 2022-06-06 at 23 39 43" src="https://user-images.githubusercontent.com/103360590/172261121-d934d25b-e20c-4407-b693-0946563bb718.png">

<img width="953" alt="Screenshot 2022-06-06 at 23 40 05" src="https://user-images.githubusercontent.com/103360590/172261174-f4fb387a-a953-4a11-9333-29fc7eab0ae0.png">

In the index.js file, we specified process.env to access environment variables, but i have not yet created this file. So i Created a file in Todo directory and name it .env.

           touch .env
           
then
           
           vi .env
           
then i Added the connection string to access the database in it, just as below:

          DB = 'mongodb+srv://<username>:
          <password>@<network-address>/<dbname>?
          retryWrites=true&w=majority'

Here is how i got the connection string


<img width="513" alt="Screenshot 2022-06-06 at 23 49 48" src="https://user-images.githubusercontent.com/103360590/172262023-1bc78862-dcf7-415f-86d2-b552abcbbb3f.png">

i then updated the index.js to reflect the use of .env so that Node.js can connect to the database.

i Simply deleted the existing content in the file, and update it with the entire code below.

this is how i deleted it

To do that using vim, follow below steps

1 Open the file with vim index.js
2 Press esc
3 Type :
4 Type %d
5 Hit ‘Enter’

i then wrote the following code below in the file.

          const express = require('express');
          const bodyParser = require('body-parser');
          const mongoose = require('mongoose');
          const routes = require('./routes/api');
          const path = require('path');
          require('dotenv').config();

          const app = express();

          const port = process.env.PORT || 5000;

          //connect to the database
          mongoose.connect(process.env.DB, { useNewUrlParser: true,                       useUnifiedTopology: 
          true })
          .then(() => console.log(`Database connected 
          successfully`))
          .catch(err => console.log(err));

          //since mongoose promise is depreciated, we 
          overide it with node's promise
          mongoose.Promise = global.Promise;

          app.use((req, res, next) => {
          res.header("Access-Control-Allow-Origin",
          "\*");
          res.header("Access-Control-Allow-Headers", 
          "Origin, X-Requested-With, Content-Type, 
          Accept");
          next();
          });

          app.use(bodyParser.json());

          app.use('/api', routes);

          app.use((err, req, res, next) => {
          console.log(err);
          next();
          });

          app.listen(port, () => {
          console.log(`Server running on port
          ${port}`)
          });
          
Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

i Started the server by using the command:

            node index.js
            
 <img width="787" alt="Screenshot 2022-06-07 at 00 14 24" src="https://user-images.githubusercontent.com/103360590/172264384-b4a370d0-a4cd-4f21-9820-5c250a3f3ab2.png">
 
You shall see a message ‘Database connected successfully’, if so – we have our backend configured. Now we are going to test it.

Testing Backend Code without Frontend using RESTful API

So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet. We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfulL API. Therefore, we will need to make use of some API development client to test our code.

In this project, we will use Postman to test our API.
Click Install Postman to download and install postman on your machine.

Click HERE to learn how perform CRUD operartions on Postman

You should test all the API endpoints and make sure they are working. For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code.

Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

Note: make sure your set header key Content-Type as application/json
  
  <img width="493" alt="Screenshot 2022-06-07 at 00 20 00" src="https://user-images.githubusercontent.com/103360590/172264876-a06e78ca-d9ca-4f61-868e-87c3761c24ae.png">

i Created a GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).
  
  <img width="493" alt="Screenshot 2022-06-07 at 00 22 05" src="https://user-images.githubusercontent.com/103360590/172265058-60c6c011-e5b4-490e-951c-1032d230e606.png">

 Optional task: Try to figure out how to send a DELETE request to delete a task from out To-Do list.

Hint: To delete a task – you need to send its ID as a part of DELETE request.

By now you have tested backend part of our To-Do application and have made sure that it supports all three operations we wanted:

. Display a list of tasks – HTTP GET request
. Add a new task to the list – HTTP POST request
. Delete an existing task from the list – HTTP DELETE request

i have successfully created our Backend, now let go create the Frontend.
  
  STEP 2 – FRONTEND CREATION
  
 Since i am done with the functionality i want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, i will use the create-react-app command to scaffold our app.

In the same root directory as my backend code, which is the Todo directory, run:

         npx create-react-app client
  
This created a new folder in my Todo directory called client, where i will add all the react code.

  Running a React App
  
Before testing the react app, there are some dependencies that need to be installed.

1 Install concurrently. It is used to run more than one command simultaneously from the same terminal window.
  
            npm install concurrently --save-dev
  
 2 Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
  
           npm install nodemon --save-dev
  
In Todo folder open the package.json file. i Changed the highlighted part of the below screenshot and replace with the code below.
  
          "scripts": {
          "start": "node index.js",
          "start-watch": "nodemon index.js",
          "dev": "concurrently \"npm run start-watch\" \"cd client && npm                 start\""
          },
  
<img width="833" alt="Screenshot 2022-06-07 at 00 34 27" src="https://user-images.githubusercontent.com/103360590/172266142-35eee4fa-fad6-4f06-b837-0c75901ccce9.png">
  
1 Configure Proxy in package.json by changing the directory to client
  
           cd client

2 Open the package.json file
  
           vi package.json
  
3 Add the key value pair in the package.json file 
  "proxy": "http://localhost:5000".
  
The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Now, i need to make ensure i am inside the Todo directory, and simply do:
  
             npm run dev

my app should open and start running on localhost:3000

Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule. You already know how to do it.

  Creating your React Components

One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component.

From my Todo directory i ran the following command
  
             cd client
  
move to the src directory
  
             cd src
  
Inside your src folder create another folder called components
  
             mkdir components
  
Move into the components directory with
  
             cd components
  
Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.
  
             touch Input.js ListTodo.js Todo.js
  
i Opened Input.js file
             
              vi Input.js
 
i then wrote in it the following code
  
            import React, { Component } from 'react';
            import axios from 'axios';

            class Input extends Component {

            state = {
            action: ""
            }

            addTodo = () => {
            const task = {action: this.state.action}

                if(task.action && task.action.length > 
            0){
                  axios.post('/api/todos', task)
                    .then(res => {
                      if(res.data){
                        this.props.getTodos();
                        this.setState({action: ""})
                      }
                    })
                    .catch(err => console.log(err))
                }else {
                  console.log('input field required')
                }

             }

             handleChange = (e) => {
             this.setState({
             action: e.target.value
             })
             }

             render() {
             let { action } = this.state;
             return (
             <div>
             <input type="text" onChange={this.handleChange} value={action} />
             <button onClick={this.addTodo}>add todo</button>
             </div>
             )
             }
             }

             export default Input
  
To make use of Axios, which is a Promise based HTTP client for the browser and node.js, i needed to cd into my client from my terminal and run yarn add axios or npm install axios.
  
i Moved to the src folder then to clients folder using he following command
  
               cd ..
  
i Install Axios
  
               npm install axios
  
  <img width="841" alt="Screenshot 2022-06-07 at 01 03 36" src="https://user-images.githubusercontent.com/103360590/172268676-3fa0ea81-e8ee-438f-89e8-fce2c91336d3.png">

<img width="841" alt="Screenshot 2022-06-07 at 01 15 18" src="https://user-images.githubusercontent.com/103360590/172269872-d4010bf0-c79b-4f62-b66a-f8cebe41e188.png">

  <img width="841" alt="Screenshot 2022-06-07 at 01 15 41" src="https://user-images.githubusercontent.com/103360590/172269891-22bd241a-cf5a-43ff-bbb1-c0800321cd06.png">
  
i encountered a series of errors when i tried to intall axios. the solution was to edit the code in the  package.json file in the client directory. i replaced the coce with the following code
  
  {
 "name": "client",
 "proxy": "http://localhost:5000",
 "version": "0.1.0",
 "private": true,
 "dependencies": {
  "@testing-library/jest-dom": "^5.16.4",
  "@testing-library/react": "^13.3.0",
  "@testing-library/user-event": "^13.5.0",
  "react": "^18.1.0",
  "react-dom": "^18.1.0",
  "react-scripts": "5.0.1",
  "web-vitals": "^2.1.4"
 },
 "scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject"
 },
 "eslintConfig": {
  "extends": [
   "react-app",
   "react-app/jest"
  ]
 },
 "browserslist": {
  "production": [
   ">0.2%",
   "not dead",
   "not op_mini all"
  ],
  "development": [
   "last 1 chrome version",
   "last 1 firefox version",
   "last 1 safari version"
  ]
 }
}
  




  
  













 



         






  

