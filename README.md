# Heroku Hosting Tutorial

Your database needs to be hosted on ElephantSQL before starting this tutorial. 

## Create Heroku Account 

1. Create a heroku account https://signup.heroku.com/dc

## Download Heroku CLI and set Heroku git remote

1. Download Heroku CLI from heroku's website https://devcenter.heroku.com/articles/getting-started-with-nodejs#set-up 

2. Make sure you have the right versions of Node, NPM, and git. The instructions for how to do this is at the bottom of the link above. 

**You only need to download the CLI and check your versions from the link about do not worry about Heroku Login quite yet. 


## Login into Heroku and create a new app. 

1. Click the “New” purple button on the right and select ‘Create new app’
2. Enter a valid name (This name will be what your URL includes so choose a name that you don’t mind having as part of the URL. Be sure to remember the name for when we setup the git remotes)
3. Hit ‘Create App’.



## Add Node.js BuildPack

1. Once your app has been created it will take you to the Deploy page. Navigate to the Settings tap.
2. Scroll down to the ‘Add build buildpack’ button on the right. Click it and then select ‘nodes’ from the examples and save your changes. 
3. A yellow box that says ‘Your new buildpack configuration will be used when this app is next deployed.’ Will appear to let you know it has been added and will set up once we 	deploy the application.  
	


## Add your config variables to your Heroku Settings

1. Still on your settings page, scroll up to the config variables section and click ‘Reveal Config Vars’
2. Enter into the first Key input the word ‘connectionString’ then in the Value input enter in the url string you received from Elephantsql when you hosted your database. 
3. Click ‘Add’. Enter in any additional key and values you have in your config.js file in your application. You want all sensitive keys and strings you don’t want uploaded to 	GitHub entered in as your config variables. If you are using auth0 be sure to enter in the domain, clientId, and clientSecret in as config variables as well. 



## Login to heroku in your terminal

1. In your terminal enter heroku login. 
2. It will ask you for your heroku credentials. Enter in your email and hit enter. Then enter in your password which will not show up as you type it will be hidden just FYI. (Both 	will be whatever email and password you created your heroku account with.)
3. Once your login is successful it will display “Logged in as <your email>”



## Check GitHub remote and setup heroku remote

1. Type git remote -v into your command line. You should see your github remotes.
	
Example: 

    origin  https://github.com/yourGithub/yourRespositoryName.git (fetch)
    origin  https://github.com/yourGithub/yourRespositoryName.git (push)

2. Now type heroku git:remote -a <yourAppName> to set a new remote to heroku. (this app name is referring to the app you made on heroku) This will allow you to use git commands to push your code to your Heroku server.

    If you type git remote -v you will now see the both heroku and github remotes.
	
Example: 
    
    heroku  https://git.heroku.com/herokuAppName.git (fetch)
    heroku  https://git.heroku.com/herokuAppName.git (push)
    origin  https://github.com/yourGithub/yourRespositoryName.git (fetch)
    origin  https://github.com/yourGithub/yourRespositoryName.git (push)




## Configure your app

In your app you will want to make the following changes:

If there are any references to localhost, these will need to be changed to your new heroku url. Your heroku url can be found in the settings tab on your heroku dashboard.
All DB strings, api keys, client secrets etc... should be put in your 'config vars' on your Heroku Dashboard as stated in one of the above sections.
To reference any of your 'config vars' inside of your app, simply use process.env.YourKeyName. Here's an example.

Example: 

        massive(process.env.database).then ((db) => {
 	            app.set('db', db);
        });

        app.listen(process.env.PORT || 3000, function() {
                console.log('listening on port', this.address().port);
        });

*Don't forget to replace your port with process.env.PORT. Capital letters are important here.
*Notice the use of the "or" ||. Using it allows you to specify both environmental variables as well as local variables. In this example the port will be specified by heroku when hosted, but default to 3000 when you're running locally.


**You will need to comment out your config.js file or remove it from your app all together. So anything in your config.js file should be entered in as a config variable on your heroku website



## Make your ENV variables work on local development: 
**IF YOU DON’T HAVE API KEYS SKIP THIS STEP

1. You can also set up your local development environment to provide your environmental variables like so.

Example: 

    apiKey=j83kgl9s8b node server.js




## Set up your package.json

If you haven’t already set your main to the file path to your server file. ./server.js or ./server/server.js if your file is in a subdirectory.

		Example: "main": "server.js",

If you don't have a start script, make one. Set your start script to node index.js (index.js or server.js or whatever you named your server file). If your server file is in a subdirectory of the root of your app, reference the path to your server file like so node server/index.js

		Example: 
			"scripts": {
   				"start": "node server.js"
 			},

Check that all your dependencies are listed. Heroku will not install anything that is not listed in your dependencies.


## Setup your Procfile

Add a file on your app's root directory called Procfile. 
Inside of your procfile enter in the following code, replacing server.js with the name of your server file. web: node server.js 

If your server file is in a subdirectory of the root, place the Procfile at the root and reference the path to your server file like so web: node server/server.js

Example: web: node server.js  OR  node server/server.js




## Auth0

Set your Auth0 console to correct redirect uri.

Example: 	passport.use(new Auth0Strategy({
            domain: process.env.domain,
            clientID: process.env.clientID,
            clientSecret: process.env.clientSecret,
            callbackURL: '/auth/callback'


Add your Heroku url to your whitelist on https://auth0.com/ 




## Launch

You can test your app locally by running your start script, passing in your ENV variables like so. (Note that you need to be in your app directory)

Example: 
    
    connectionString=‘elephantSQLconnectionString’ node server.js

If you are confident that your app is running ok at this point then we are ready to push the app to heroku. Add, commit, and push your app to Heroku. 

Example: 

    git add .
    git commit -m "message"
    git push heroku master

After it is finished building you can check to see the status of your app by typing in heroku logs --tail. 

Example: 

    heroku logs --tail

Either your logs will report "state changed from starting to up" or "state changed from starting to crashed". If the app crashed, you will have to debug. Otherwise you are good to go! Find your url on the settings tab of your heroku dashboard online.



Any time you make changes to your code you will need to add, commit, and push your changes to heroku master in order for them to update. 

## ADDITIONAL RESOURCES: 
https://devcenter.heroku.com/categories/nodejs
https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction

