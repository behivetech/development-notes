# Heroku Notes

## Deploy
Create an app
```
heroku create
```
Deploy your code
```
git push heroku master
```
Starting an instance
```
heroku ps:scale web=1
```
Shortcut to open the app on the web
```
heroku open
```

## Logs
View the Heroku logs of the app
```
heroku logs --tail
```

## Heroku Config
Set .env vars
```
heroku config:set TIMES=2
```

View Config
```
heroku config
```

## Scaling the app
Think of a dyno as a lightweight container that runs the command specified in the Procfile.

Check how many dynos app is running
```
heroku ps
```

Scale number of dynos to zero
```
heroku ps:scale web=0
```

Scale number of dynos to 1 or more
```
heroku ps:scale web=1
```

## Provision add-ons
Add-ons are third-party cloud services that provide out-of-the-box additional services for your application, from persistence through logging to monitoring and more.

Adding add-ons
```
heroku addons:create <add-on name>
```

Listing add-ons
```
heroku addons
```

View add-on
```
heroku addons:open <add-on name>
```

NOTE: `papertrail` is an add-on that will keep the logs which may be good to have

## Heroku Console
Open a console
```
heroku run bash
```

## Postgres database
Create a postgres databas
```
heroku addons:create heroku-postgresql:hobby-dev
```

Connect to database
```
heroku pg:psql
```

## package.json setting
Should add which version of node the app has been developed on in the package.json file...
```
  "engines": {
    "node": "13.x"
  }
```

## Define a Procfile
Use a Procfile, a text file in the root directory of your application, to explicitly declare what command should be executed to start your app.

The Procfile in the example app you deployed looks like this:
```
web: node index.js
```

This declares a single process type, web, and the command needed to run it. The name web is important here. It declares that this process type will be attached to the HTTP routing stack of Heroku, and receive web traffic when deployed.

Procfiles can contain additional process types. For example, you might declare one for a background worker process that processes items off of a queue.


