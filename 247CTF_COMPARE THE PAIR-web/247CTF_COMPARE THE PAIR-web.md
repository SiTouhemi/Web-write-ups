# COMPARE THE PAIR (Web)

## Intro  
This is my write-up for the Web challenge "Secured Session" on the CTF site 247CTF.com.

## Challenge Details  
The challenge involves finding a way to bypass the login logic using something related to `md5`. 

## Steps to Solve

### 1. Access the Application  
Start by accessing the web application provided for the challenge.

### 2. Analyze the Provided PHP Code
php
<?php
require_once('flag.php'); $password_hash = "0e902564435691274142490923013038"; $salt = "f789bbc328a3d1a3"; if(isset($_GET['password']) && md5($salt . $_GET['password']) == $password_hash){ echo $flag; } echo highlightfile(_FILE, true); ?> ```

The code takes the $salt and concatenates it with our $password input, then md5 hashes it to compare with the $password_hash.

We have no information about what the password might be, and brute-forcing it could take a long time.

The vulnerability occurs in the == comparison.

### 3. Understanding the Vulnerability
In PHP, the comparison operator == checks two values for equality, performing type juggling. This means PHP attempts to convert the values to a common type before making the comparison.

Key Points About == in PHP:
Type Juggling: PHP automatically converts operand data types when using ==. For example: php if (5 == "5") { echo "Equal"; } Here, "5" (a string) is converted to an integer, so the comparison returns true.

Loose Comparison: == is known as a "loose comparison." For instance: php if (0 == false) { echo "Equal"; } In this case, 0 is equivalent to false, returning true.

Common Pitfalls:

Comparing different types can yield unexpected results: php if ("0" == false) { echo "Equal"; } Here, the string "0" is treated as false, leading to a true comparison.
Additionally, the format of $password_hash resembles a large number starting with 0e.... When 0e1234 is compared using ==, PHP treats it as the number 0:

if ("0e1234" == 0) {
    echo "Equal";
} else {
    echo "Not Equal";
}
Output: "Equal"

### 4. Exploiting the Vulnerability
The vulnerability is clear: we need to find a password such that when concatenated with $salt, its md5 hash generates a string that appears like a large number (starts with 0e followed by digits).

Here's a Python script to help with this:

from itertools import product  
import hashlib

for x in range(0, 10):
    for combo in product("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789", repeat=x):
        result = hashlib.md5(("f789bbc328a3d1a3" + ''.join(combo)).encode()).hexdigest()
        if result.startswith("0e") and result[2:].isdigit():
            print("f789bbc328a3d1a3" + ''.join(combo))
Any resulting password can be sent to the server as ?password=xxxxxx. Finally, we can retrieve the flag!


### Resources 
https://www.php.net/manual/en/language.types.type-juggling.php
https://github.com/abhaynayar/obsidian/blob/master/labs/web/247ctf.md
