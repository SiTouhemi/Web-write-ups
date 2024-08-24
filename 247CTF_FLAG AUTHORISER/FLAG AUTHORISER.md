# FLAG AUTHORISER (Web)

## Intro

This is my write-up for the Web challenge "FLAG AUTHORISER" on the CTF site 247CTF.com.

## Challenge Details

The challenge involves finding a way to upgrade your access from an anonymous user to an admin.

## Steps to Solve

### 1. Access the Application

Start by accessing the web application provided for the challenge.

### 2. Analyze the Provided  Code



Reviewing the code shows us that we are looking at a Flask web app that uses JWT (JSON Web Token). Visiting /flag will give us our token with identity='anonymous'. Our goal is to find how to change the anonymous JWT data to admin.

##Steps to Solve
###1. Obtaining a Valid Cookie
Visit /flag and copy the access_token, then visit https://jwt.io to see what data we got.

Let's change the identity to admin and replace the token with the new one.

Of course, it doesn't work 😂, so let's Google it.

We know that there's a secret key required for the token, which is why we got an error after trying the admin token.

###2. Cracking the Token
With some Google searching, I found that cracking the secret key is possible using John the Ripper.

Check here for more info: https://www.freecodecamp.org/news/crack-passwords-using-john-the-ripper-pentesting-tutorial/

Place the token in a file and run this command (247CTF mentioned that we can use rockyou.txt as a wordlist to crack it faster instead of brute-forcing):

bash
Copier le code
john --wordlist=/usr/share/wordlists/rockyou.txt jwt.txt  # token stored in jwt.txt
###3. Forging the Token
Take the token, put it again in https://www.jwt.io.

We know the identity needs to be admin, so set that up in the parameters.

Add the secret.

Place the new token in the /flag route, and you'll get the flag.

###Resources
https://www.freecodecamp.org/news/crack-passwords-using-john-the-ripper-pentesting-tutorial/
