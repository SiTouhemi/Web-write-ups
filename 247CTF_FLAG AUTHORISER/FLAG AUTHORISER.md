\# FLAG AUTHORISER (Web)

\## Intro

This is my write-up for the Web challenge \"FLAG AUTHORISER\" on the CTF
site 247CTF.com.

\## Challenge Details

The challenge involves guessing a way to upgrade your access from an
anonymous user to an admin

\## Steps to Solve

\### 1. Access the Application

Start by accessing the web application provided for the challenge.

\### 2. Analyze the Provided PHP Code

from flask import Flask, redirect, url_for, make_response,
render_template, flash

from flask_jwt_extended import JWTManager, create_access_token,
jwt_optional, get_jwt_identity

from secret import secret, admin_flag, jwt_secret

app = Flask(\_\_name\_\_)

cookie = \"access_token_cookie\"

app.config\[\'SECRET_KEY\'\] = secret

app.config\[\'JWT_SECRET_KEY\'\] = jwt_secret

app.config\[\'JWT_TOKEN_LOCATION\'\] = \[\'cookies\'\]

app.config\[\'DEBUG\'\] = False

jwt = JWTManager(app)

def redirect_to_flag(msg):

flash(\'%s\' % msg, \'danger\')

return redirect(url_for(\'flag\', \_external=True))

\@jwt.expired_token_loader

def my_expired_token_callback():

return redirect_to_flag(\'Token expired\')

\@jwt.invalid_token_loader

def my_invalid_token_callback(callback):

return redirect_to_flag(callback)

\@jwt_optional

def get_flag():

if get_jwt_identity() == \'admin\':

return admin_flag

\@app.route(\'/flag\')

def flag():

response = make_response(render_template(\'main.html\',
flag=get_flag()))

response.set_cookie(cookie, create_access_token(identity=\'anonymous\'))

return response

\@app.route(\'/\')

def source():

return \"

%s

\" % open(\_\_file\_\_).read()

if \_\_name\_\_ == \"\_\_main\_\_\":

app.run()

Reviewing the code shows us that we are looking at Flask webapp wich
uses a JWT (json web token) . Visitng /flag will give us our token with
identity=\'anonymous\' , our goal is finding how to change the anonymous
jwt data to admin .

\## Steps to solve

1\. obtaining a valind cookies

Visit /flag and copy the acces_token and visit https://jwt.ioto see what
data we got .

![](./image1.png){width="6.531944444444444in"
height="2.5256944444444445in"}

Lets Change identity to admin and replace the token with the new one .

OFC it doesn't work xd , lets google .

![](./image2.png){width="6.531944444444444in" height="3.025in"}

We know that there\'s a secret key required for the token, which is why
we got an error after trying the admin token.

\## 2. Cracking the token

With some goole search , found that cracking the secret key is possible
using jhontheripper .

Check here for more info :
<https://www.freecodecamp.org/news/crack-passwords-using-john-the-ripper-pentesting-tutorial/>

Place the token in a file and run this command (247CTF mentioned that we
can use rockyou.txt as a wordlist to crack faster instead of brute
forcing ).

john \--wordlist=usr/share/wordlists/rockyou.txt jwt.txt //token stored
in jwt.txt

### ##Forging the token

Take the token, put it again
in [https://www.jwt.io](https://www.jwt.io/)

We know we need the identity needs to be admin so set that up in the
parameters.

Add the secret.

![](./image3.png){width="6.531944444444444in"
height="2.9097222222222223in"}

Place the new token in the /flag and ull get the flag

###resources

<https://www.freecodecamp.org/news/crack-passwords-using-john-the-ripper-pentesting-tutorial/>
