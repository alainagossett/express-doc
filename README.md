# Build an Express app  
### Express is for the back end  


1. Navigate to a location on your computer where you want to build the project. Change into that folder
```
mkdir Express
cd Express
```
2. Create an entry poit file in your project
```
touch server.js
```
 - add a .env file for Git to ignore
 ```
 touch .env
 ```
3. Initialize package.json
```
npm init -y
```
4. Install dependencies
```
npm i express ejs mongoose dotenv
```
  - other dependencies include:
    * axios
    * cors
    * method-override
    * morgan

5. If using MongoDB, retrieve connection URL string
> Cluster >> Connect >> Connect your application >> Copy/Paste the URL
```
DATABASE_URL=mongodb+srv://admin:<PASSWORD>@cluster0.2clac.mongodb.net/<DATABASENAME>?retryWrites=true&w=majority
```
  - put this in your dotenv file to protect your database's password

6. Add your port to your dotenv file
```
PORT=3000
```
7. Create your express boilerplate inside of server.js
```
// Require Dependencies  

// Initialize the App  

// Connect and Configure DB (MongoDB)  

// Mount Middleware  

// Define Routes  

// Tell the app to listen
```
  - **Make sure you also require and configure the dotenv package**
  ```
    require('dotenv').config();
  ```
8. Connect to MongoDB by passing in the database URL
```
mongoose.connect(process.env.DATABASE_URL);
```
9. Mount the middleware and tell the app to listen
```
app.use(express.json());
app.listen(PORT, () => console.log(`listening on PORT ${PORT}`))
```
10. Add new directories to your project to outline MVC
```
mkdir models views controllers
```
11. Add the appropriate files to models and controllers named "[resourceName].js" **singular for models, plural for controllers**
```
touch /controllers/apps.js
touch /models/app.js
```
12. Define a schema in your model for performing full CRUD in your MongoDB collection
```
// inside /models/app.js

const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const thingSchema = new Schema({
    name: String,
    description: String,
    code: { type: String, required: true },
}, { timestamps: true });

const thing = mongoose.model('Thing', thingSchema);

module.exports = thing;
```
  - timestamps are not required, but best practice
  - you will require this schema in your controllers file

13. In your controller file set up your router object and define the CRUD routes and make sure they all respond with res.render() or res.redirect()
  - Be sure to also import Router so you can reference it in server.js
  - Think INDUCES
```
// inside /controllers/apps.js

const express = require('express');

const Thing = require('../models/thing');

const thingRouter = express.Router();

// INDEX ROUTE
thingRouter.get('/', (req, res) => {
    res.send('hello world')
})

// NEW ROUTE
thingRouter.get('/things/new', (req, res) => {
    res.render('new.ejs')
})

// DELETE ROUTE
thingRouter.delete('/things/:id', asyn (req, res) => {
    try {
        await Thing.findByIdAndDelete(req.params.id)
    } catch (error) {
        res.status(400).json(error)
    }
})

// UPDATE ROUTE
thingRouter.put('/things/:id', asyn (req, res) => {
    try {
        await Thing.findByIdAndUpdate(req.params.id, req.body, { new: true })
    } catch (error) {
        res.status(400).json(error)
    }
})

// CREATE ROUTE
thingRouter.post('/things', async (req, res) => {
    try {
        await Thing.create(req.body)
    } catch (error) {
        res.status(400).json(error)
    }
})

// SHOW ROUTE
thingRouter.get('/things/:id', asyn (req, res) => {
    try {
        await Thing.find(req.params.id)
    } catch (error) {
        res.status(400).json(error)
    }
})

module.exports = thingRouter;
```
  - **Be sure to export the module at the bottom so you can reference it in server.js**

14. In your views directory add all necessary HTML templates to correspond with your routes
### eg:
```
// inside /views/new.ejs

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Create a new Thing</title>
  </head>
  <body>
    <h1>New Thing page</h1>
    <form action="/things" method="POST">
      Name: <input type="text" name="name" /><br />
      Description: <input type="text" name="description" /><br />
      Code: <input type="text" name="code" /><br />
      <input type="submit" value="Create Thing" />
    </form>
  </body>
</html>
```
- Use squids for embedding code blocks in your HTML files
> <% %> is for JS codeblocks  
<%= %>  is for referencing data points (like ids and model attributes)
- In your views templates, you'll use form tags. 
> Forms can only perform GET and POST actions, so you'll use **method-override**  

```
// in your views template

<form action="/things/<%=index; %>?_method=DELETE" method="POST"></form>

<form action="/things/<%=index%>?_method=PUT" method="POST"></form>
```
```
// in server.js

const methodOverride = require('method-override');

app.use(methodOverride('_method'));
```
- You'll have anchor tags with links matching your routes
- Your input tags will signify actions and have a type of **submit**

## Common Boilerplate code:
### This would go in server.js
```
const express = require('express');
const mongoose = require('mongoose');
const appRouter = require('./controllers/app');

require('dotenv').config();

const { PORT = 3000 } = process.env;

const app = express();

mongoose.connect(process.env.DATABASE_URL);
mongoose.connection
    .on('open', () => console.log('Connected to MongoDB'))
    .on('close', () => console.log('Disconnected from MongoDB'))
    .on('error', () => console.log('An error occurred: ' + error))

app.use(cors());
app.use(morgan('dev));
app.use(express.json());

app.use('/', appRouter);

app.listen(PORT, () => console.log(`App is listening on port ${PORT}`));

```