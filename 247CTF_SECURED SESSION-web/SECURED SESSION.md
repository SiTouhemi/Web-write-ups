Secured Session (web)

# Intro

This is my write-up of a Web challenge Secured Session on the CTF
site [247CTF.com](https://247ctf.com/)

## Challenge Details

If you can guess our random secret key, we will tell you the flag
securely stored in your session.

## Steps to Solve

### 1. Access the Application:

-   Start by accessing the web application provided for the challenge.

### 2. Read the php code provided

import os
from flask import Flask, request, session
from flag import flag

app = Flask(__name__)
app.config['SECRET_KEY'] = os.urandom(24)

def secret_key_to_int(s):
    try:
        secret_key = int(s)
    except ValueError:
        secret_key = 0
    return secret_key

@app.route("/flag")
def index():
    secret_key = secret_key_to_int(request.args['secret_key']) if 'secret_key' in request.args else None
    session['flag'] = flag
    if secret_key == app.config['SECRET_KEY']:
      return session['flag']
    else:
      return "Incorrect secret key!"

@app.route('/')
def source():
    return "
%s
" % open(__file__).read()

if __name__ == "__main__":
    app.run()
After After analysing the code we notice that its all about cookies and
a random key so , lets check our cookie :

![](./image1.png)

### 3. checking provided information

Notice that theres no session cookies . Lets check /flag:

![](./image2.png)

It says Incorrect Key! We know that the key is stored in our cookie
session , lets analyze it than .

After googling abouf Flask sessions I found this website :
<https://www.bordergate.co.uk/flask-session-cookies/> .

I realized that flask cookies are same as Json Web Tokens (JWT) were its
data separted by a dot (' . ') .

Found this website to analyse JWT cookie <https://jwt.io>
![](./image3.png)

By pasting the cookies you will see its content and flag data clearly
base64 encoded .

Decode it and booom! \$ echo
\"MjQ3Q1RGe2RhODA3OTVmOGE1Y2FiMmUwMzdkNzM4NTgwN2I5YTkxfQ==\" \| base64
-D

247CTF{xxxxxxxx}
