Secured Session (Web)
This is my write-up for the Web challenge "Secured Session" on the CTF site 247CTF.com.

Challenge Details
The challenge involves guessing a random secret key to retrieve a flag securely stored in your session.

Steps to Solve
1. Access the Application
Begin by accessing the web application provided for the challenge.

2. Analyze the Provided PHP Code
The challenge provides the following Python Flask code:



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
    return "<pre>%s</pre>" % open(__file__).read()

if __name__ == "__main__":
    app.run()
Analyzing the code, we see that it relies on cookies and a random key. Let’s check our cookies:

image1.png

The cookies don’t contain the key. Let’s try visiting /flag to see the response.

3. Check the Response
When visiting /flag, we get the following response:



The message indicates that the key is incorrect. Since the key is stored in the session cookie, let's analyze the cookie.

After researching Flask sessions, I discovered they are similar to JSON Web Tokens (JWT), where data is separated by dots (.). To analyze the JWT cookie, use a tool like jwt.io.



By pasting the cookie into the JWT.io decoder, we can view its contents, which includes the flag data, base64-encoded. Decode the base64 string to reveal the flag:

bash

$ echo "MjQ3Q1RGe2RhODA3OTVmOGE1Y2FiMmUwMzdkNzM4NTgwN2I5YTkxfQ==" | base64 -d
The decoded output will be the flag:


247CTF{xxxxxxxx}
