AisWebService
=============

A system that provide Academic Information we name it AIS Academic Information System.

# Clone This Bundle

Go to your working directory with your terminal (command line)

```
$ cd /var/www/html/
```

Then run the Git command to clone the bundle, if not yet install Git on your system just run this command (ubuntu):

```

$ sudo apt-get install git
```

for more detail explanation please visit this page:

[Setting up Git](https://help.github.com/articles/set-up-git/)

```
$ git clone https://github.com/theredfoxfire/ais-web-service.git
```

Wait till the clone process is done.

# Preparing Your Kit

First thing you need is Composer.

Go to your app directory, then run the comand to install Composer

```
$ cd ais-web-service/

$ curl -sS https://getcomposer.org/installer | php
```
For more information about Composer you may visit the link below

[Getting started with Composer](https://getcomposer.org/doc/00-intro.md)

Ok, after Composer installed on your working directory then update the vendor of the app by run this command

```
$ php composer.phar update
```

See the progress usually it's takes several minutes to be done.

# Setting Up the Web Service

Ok, So everything is installed on our kit. To setting up the web service, first thing to do is

update your parameters.yml setting up with your environment

```yaml

# this file is located in app/config/parameters.yml

parameters:
    database_host: 127.0.0.1
    database_port: null
    database_name: your-dbname
    database_user: your-dbusername
    database_password: your-dbpassword
    mailer_transport: smtp
    mailer_host: 127.0.0.1
    mailer_user: null
    mailer_password: null
    secret: 4ec56e086e1a7834c546c1e7f637c3cb2390b21e

```

Update the database structure by run this command:

```
$ php app/console doctrine:database:create

$ php app/console doctrine:schema:update --force
```

Well done! next is we try to inserting dummy user to our database. Just run this command:

```
$ php app/console doctrine:fixtures:load
```

Yes we have one user that ready to taste the API!

by default the user atrribute is:

```
username: user
password: 12345

```

# Start Consuming The API

Before you get continue it's recomended to run app server by type this command:

```
$ php app/console server:run

it will running the app on:

http://127.0.0.1:8000
```

## If everything done, you may find the API doc by access this url:

```
 http://127.0.0.1:8000/service/api/doc
```

You can find out the available url API by type this command:

```
$ php app/console debug:route | grep api
```

let's say we want to access the user API:

```
 http://127.0.0.1:8000/api/v1/users.json
```
Oops you get error message, because you need to be authenticated to access the API

```
{"error":"access_denied","error_description":"OAuth2 authentication required"}
```
## To access the API we need to registering OAuth client into our web service, just run this command:

```
$ php app/console ais:oauth-server:client:create --redirect-uri="FILLED_WITH_YOUR_CLIENT_HOST_URL" --grant-type="authorization_code" --grant-type="password" --grant-type="refresh_token" --grant-type="token" --grant-type="client_credentials"
```

that command will give you a result something like:

```
Added a new client with public id 1_423usr7ce4aocg8g0ckkgsgw8oosss8kggwgoow0k40w08cg4w, secret 3fxs2hwcd2uc888wg8wskcwgcc0084ks440o8ko4ow480okg84
```

## Get Authorization code, just access from your browser URL like:

```
YOUR_WEB_SERVICE_PROVIDER_HOST/oauth/v2/token?client_id=CLIENT_ID&client_secret=CLIENT_SECRET&grant_type=password&username={USERNAME}&password={PASSWORD}

so it may look like:

 http://127.0.0.1:8000/oauth/v2/token?client_id=1_423usr7ce4aocg8g0ckkgsgw8oosss8kggwgoow0k40w08cg4w&client_secret=3fxs2hwcd2uc888wg8wskcwgcc0084ks440o8ko4ow480okg84&grant_type=password&username=user&password=12345
```

Yes the web service give us an access token to access the API. 

```json
{"access_token":"NzIxODYzNmVmN2I1ZTFhZWExOWVmN2RiYTNjYjAwZjU2ZGQ1ZDA0NjMwNDM1MWM3OGY5ZTEyODcwOGRkYmEwNg","expires_in":3600,"token_type":"bearer","scope":"user","refresh_token":"ZDUyNTEzMDBjYzNiMDcwODY2ODhmOThlMDJlZTM2NzE4YWQzODUzOTllMWViNzcxMDk4ZDg1OTk2Y2QwNDJkYw"}
```

Now we may access the API by using that token. Let's try it to access the user API:

```
 http://127.0.0.1:8000/api/v1/users.json?access_token=YOUR_ACCESS_TOKEN

it look like:

 http://127.0.0.1:8000/api/v1/users.json?access_token=ODNmMmEwYTIzMDcyYjA2OGEwZGE1MzliZTc4MDEzYmNiY2NiNmRhMWZhN2UzZjg2NWU3NDg2MGZjMWNiZTVlYw
```

Horay! we get the users json data

```json
[{"id":1,"username":"user","password":"BZM27f7KTL924kFQGM8v4285XY115okFslPfajOWLmzSgzTg\/q7F4q6P2xdLvgLRRU4S12qyUAezGnJ\/sbDTZA==","salt":"eoel4airrj4gs4owookkgccw0cwcw4","nama":"John","is_active":true,"roles":[],"access_token":[],"refresh_token":[],"auth_code":[],"apikey":"7dnvqj2s1log08cwwswkwksk4ccgck4"}]
```

Well take attention the token will be expired in one hour, so we need to refresh the token before it's expired.

This simple way to get new token:

```
PROVIDER_HOST/oauth/v2/token?client_id=CLIENT_ID&client_secret=CLIENT_SECRET&grant_type=refresh_token&refresh_token=REFRESH_TOKEN

it will look like:

 http://127.0.0.1:8000/oauth/v2/token?client_id=1_423usr7ce4aocg8g0ckkgsgw8oosss8kggwgoow0k40w08cg4w&client_secret=3fxs2hwcd2uc888wg8wskcwgcc0084ks440o8ko4ow480okg84&grant_type=refresh_token&refresh_token=NTUzZjAzNzRlZjE1MGE1ZTBiZGIzZWFhNjFjMzM4YmQyMTI2MTZjODQ3OWEwM2YxZDRiZmYzMTg4N2M4ZTMzYQ
```

And here is the new access token:

```json

{"access_token":"ZDY1OWFiODQ3ZjkwYmRlMGVlOGQwZjM2OGZjNmRiNjNiNmVlNzU1MzVlYmQzYjIwNDNlOWE0M2ZjNmJjZGMzOQ","expires_in":3600,"token_type":"bearer","scope":"user","refresh_token":"ZDgzNWI2N2I0MGI2MWM2MDdjY2E2NTc5ZDIwMmEwMTg2MWU2ZDIwMTkyZjJkNGE2NmZlNTIxZmI3YjQ3MGYwYQ"}

```

Ok well its litle complex and if you want learn more about consuming API better for you to visit this page:

[OAuth2 Explained: Part 3 - Using OAuth2 With Your Bare Hands](http://blog.tankist.de/blog/2013/07/18/oauth2-explained-part-3-using-oauth2-with-your-bare-hands/)

## Grab Your Coffee

If you find a typo or miss a thing let me know by vizzlearn@gmail.com
