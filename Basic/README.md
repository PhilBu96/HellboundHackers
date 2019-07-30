###Basic 1
Similiar to HTS 1, looking in the source reveals the following comment: `<!-- When you pet me, I purr. -->` From there, the solution is pretty obvious.

###Basic 2
For this challenge, we are asked to find the source of an iframe - lucky for us, iframes have a "src" element that lables their origin. If you inspect the element, we find the following code:
`<iframe border="0" height="250" width="500" align="center" name="content" src="../basic1/b2/index.php" frameborder="0" scrolling="yes"></iframe>`

###Basic 3
In a nice break, this challenge says exactly what it is looking for:`Wrong user_agent, bwh3_user_agent wasn't found` Easiest way to tackle this is to download a user-agent switching extension and set your user agent as requested.

###Basic 4
This challenge requires a bit of guess-work - we know we are looking for a htpasswd file, but that it is not in basic4's directory. First thought is to maybe scan in either direction: basic3 returns a 404, but https://www.hellboundhackers.org/challenges/basic5/htpasswd.php gives us our challenge's password.

###Basic 5
I've read the introduction like six fucking times and have no fucking idea what it is trying to say. It's something with a wildcard. Remember the username is an email address. Just fuck this challenge, here is the answer I got after a lot of trial and error:
```
*@*:*
```

###Basic 6
First we need to find the location of these files - I used DirBuster but any url fuzzer will work. It should find a logs/ directory with logs.txt and track_logs.php.

For the commands, make sure you place a space between $ and the actually command. In addition, use a+x for chmod, not the number representation. For the rm commands, -f is not required.

###Basic 7
Looking at our cookies, we see we have two cookies: username = sam, and password = jillisdead. According to the challenge, "he decrypted it from ASCII encryption." I'm not sure what ASCII encrpytion is, but I guessed it meant we needed to change our username cookie. I initially tried converting 'sam' to hex, but it ends up being binary that is the correct encryption.

Once we get past that, the password we can break with a simple SQL injection:
```
' or 1=1--
```

###Basic 8
Entering a fake password reveals a nice clue:
```
SQL Query Error: SELECT * FROM family_db WHERE password='asd' 
```

Looking at the source reveals another helpful hint:
```
<!-- ?sql_query -->
```

Worth a shot to see if secure_php is accepting this as an argument we can post:
```
/secure-area.php?sql_query=SELECT * FROM family_db
```

###Basic 9
```
<!-- The Admin also said something about null or something like that, and he made a special script that it cannot be injected by the url. but i don't know what he is talking about (why dont you find out)-->
```

Key to this is using a poisoned null byte (%00, which causes badly-coded php to drop the rest of the string). The page you are delivered to has the username and password in the source.

###Basic 10
Proxy based problem. Going to the blocked page will reveal the requested range in the comments.

###Basic 11
Simply create a user-agent string that contains the string "HellBoundHackersOS" to pass this challenge.

###Basic 12
We can abuse the file-inclusion exploit. Looking at the URL:
```
index.php?page=challenges.php
```

Since we can guess there is an .htaccess file in the protected folder:
```
index.php?page=protected/.htaccess
```

This will give you a DES hash that you can crack with John the Ripper. Make sure you download a word-list and don't brute-force it.

###Basic 13
Simply change the value of Frank's entry to George. Simple with Chrome.

###Basic 14
The password for this is revealed in the source code.

###Basic 15
The hint text should give a pretty clear idea - look at robots.txt to see where our hidden file is.

###Basic 16
Well let's try our tried and true SQL injection:
```
' or 1=1--
```

###Basic 17
A java applet this time to vary things up. Download basic17.class and use a java decompiler to view its source:
```
	public basic17()
    {
        file4 = "baysick-seventeen.complete.php?pass=";
        inputLine = new TextField(15);
        file2 = "test";
        add(inputLine);
        inputLine.addActionListener(this);
        file2 = "challenges";
    }

    public void actionPerformed(ActionEvent actionevent)
    {
        String s = inputLine.getText();
        if(s.equals("ifYOU'REhereYOU'REelite"))
        {
            URL url = null;
            String s1 = "http://www.hellboundhackers.org/";
            String s2 = "basic17";
            try
            {
                url = new URL(getDocumentBase(), s1 + "/" + file2 + "/" + s2 + "/" + file4 + new String("elite"));
            }
            catch(MalformedURLException malformedurlexception) { }
            getAppletContext().showDocument(url);
        } else
        {
            inputLine.setText("not yet");
        }
    }
```

Since this is Java, basic17 will be called first as the constructor, rendering the following values that we care about:
```
    //file4 = baysick-seventeen.complete.php?pass=
    //file2 = challenges
```

From here, it is simple enough to go through the url and manually insert the correct values:
```
	//http://www.hellboundhackers.org/challenges/basic17/baysick-seventeen.complete.php?pass=elite
```

###Basic 18
This is a very tricky challenge but not so difficult. All we have to do is to inject an SQL statement into the URL.
Simply write an UNION-Statement next to the "id=1" (for example) which selects articles 1-5 from the table "Articles". This is a "blind" SQL injection as you can read here: https://www.owasp.org/index.php/Blind_SQL_Injection

###Basic 19
Just start the developer tools of your web browser and go to the (in Google Chrome) "network" tab. When you are recording (red dot in the upper left corner) simply reload the page. Now you can see all the files which are delivered by the server. Click on the "basic19/" one. Now switch to "headers" and look for the password and the encryption method. The rest is very simple ;-)

###Basic 20
This is a little tricky one and when you think, you made it. YOU NOT! Your first attempt should be login as the given "fire" user. Then edit the "whoami" cookie with a plugin or the developer tools to "admin" and reload the site. Then you'll get a nice message which says:
```
Nice try, but that isn't the answer we were looking for, there is another way to bypass this login,
maybe it's a MySQL login that uses cookies...
```
Well, lets do this ;-) Just create an SQL statement as the new "whoami" value for the cookie which leads you to "true" like this:
```
admin' or '1=1'
```
By the way: It doesn't matter if you take admin as a user or not. The crucial thing here is 1=1, which always makes it true.
