# Reverse-engineering

Reverse Engineering Code

This is for homework assignment #14

Reverse engineer the starter code provided and create a tutorial for the code.

In the Develop folder, there is starter code for a project. Begin inspecting the code to get an understanding of each file's responsibility. Then, in a Google Doc, write a tutorial explaining every file and its purpose. If one file is dependant on other files, be sure to let the user know.

The server.js file
./homework-unit-14
└── server.js
The server.js file is your entry point into this Node application. It sits in the root of the project folder and contains the JavaScript coded needed for the Express server to work, serve files and make the page routing work.

The routes/ folder
routes/
├── api-routes.js
└── html-routes.js

0 directories, 2 files
The routes folder contains 2 javascript based source files. One for HTML (the page routes) and one for the API (the API routes) routes. Each file uses an annonymous function (passing in 1 parameter ) upon export so that when we require them in the server.js file we can pass the app constant that we made using express().

Api-routes files contain the routes for the api and the expected and accepted logic for each HTTP protocol as needed. The same can be said for the html-routes.js file.

The public/ folder
public/
├── js
│   ├── login.js
│   ├── members.js
│   └── signup.js
├── login.html
├── members.html
├── signup.html
└── stylesheets
    └── style.css

2 directories, 7 files
The public folder contains all of the files necessary for the front end. This includes all the client side JavaScript, CSS and HTML needed to display the site. The members.html page in this case, acts as the index page when a user is logged in vs when the signup.html page being the default index page when a user is logged out.

We can be sure of this because of the code in the routes/html-routes.js file that references a HTTP GET Request and where to send response.

This code reroutes and index or root route to the /members endpoint if they are logged in, and displays the signup.html page if they are not logged in.

app.get("/", function(req, res) {
  // If the user already has an account send them to the members page
  if (req.user) {
    res.redirect("/members");
  }
  res.sendFile(path.join(__dirname, "../public/signup.html"));
});
We can further check this by checking the API route for the /members page to see that upon sucessfull login, the user is sent to the members.html page and a user that is not logged in gets automatically redirected due to the isAuthenticated middleware that is in the route. This middleware is dependent upon the config/isAuthenticated.js file.

app.get("/members", isAuthenticated, function(req, res) {
  res.sendFile(path.join(__dirname, "../public/members.html"));
});
The routes/html-routes.js file is dependent upon the config/isAuthenticated.js file.

The config/ folder
config/
├── config.json
├── middleware
│   └── isAuthenticated.js
└── passport.js

1 directory, 3 files
The config folder contains the Sequelize configuration file (config.json) that holds all of your database's sensitive credentials (be sure to only add environment variables and never add real world credentials into this file if your code is public).

The middleware/isAuthenticated.js file is an important custom middleware file. With this file, we can double check the session on every call to a protected route on the site to make sure that the route is only available to those users that are logged in! If they are not logged in, they get redirected back to the root of the site "/" which then redirects them to the signup.html page.

// This is middleware for restricting routes a user is not allowed to visit if not logged in
module.exports = function(req, res, next) {
  // If the user is logged in, continue with the request to the restricted route
  if (req.user) {
    return next();
  }

  // If the user isn't logged in, redirect them to the login page
  return res.redirect("/");
};
passport.js is needed in order to communicate with PassportJS, a 3rd party npm module being used as a way of authenticating users. This code basically tells passport, that out of its 500+ ways of authenticating a user, we want to use the Local strategy and that we will be handling the user authentication locally.

passport.js is dependent upon the /models folder because it needs to be able to connect to the index.js file.

The models/ folder
models/
├── index.js
└── user.js

0 directories, 2 files
The models folder contains two files. The user.js file which handles the authentication of users when logging in and the encryption of user passwords by salting and hashing them before saving the credentials to the database, and the index.js file which contains the connection code needed for us to use our database in the app.

models/index.js file requires the config/config.json file. Sequelize will fail without this config file.
