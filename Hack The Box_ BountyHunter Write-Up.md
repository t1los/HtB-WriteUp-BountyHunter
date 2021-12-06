# Hack The Box: BountyHunter Write-Up



**Enumeration:**

Firstly, we perform an NMap scan on the target.
![](https://i.imgur.com/CsqO1Z7.png)

We begin browsing and we are met with the following page:
![](https://i.imgur.com/OJPnUnp.png)


After that we start our gobuster scan.
![](https://i.imgur.com/np0H244.png)


Clicking on "Portal" we are led to this page:
![](https://i.imgur.com/vhrlEGD.png)

Further exploring we find this interesting webpage:
![](https://i.imgur.com/JFFiWZX.png)

The starting page gave us a useful information. **"Can use Burp"**
We fire up our burpsuite and intercept the request after we send some data.
![](https://i.imgur.com/F40WRNJ.png)

We send the data to the decoder and decode them using the "Decode as" option where we select Base64.
![](https://i.imgur.com/QErb4UF.png)
We get the XML Encoded version of the fields.

**Command Injection:**

We will attempt to read a file by injecting an XXE file after Base64 encoding it.



```
<?xmlversion="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE data [
<!ENTITY file SYSTEM "file:///etc/passwd"> ]>
<bugreport>
    <title>test</title>
    <cwe>test</cwe>
    <cvss>test</cvss>
    <reward>&file;</reward>
</bugreport>
```
![](https://i.imgur.com/agUAEoJ.png)

We also need to URL encode the data.
![](https://i.imgur.com/8ZrACTT.png)

Sending the request we receive:
![](https://i.imgur.com/YS68KDo.png)

We see a user named Development but we don't have a password yet so we target the db.php file using the same method.
![](https://i.imgur.com/CeR49Ko.png)
We get this hash and decode it, again using the burp decoder.
![](https://i.imgur.com/ZBsZT3Y.png)

We try to ssh using the exact credentials but it doesn't give us access so we try development as the user name and we are in!
![](https://i.imgur.com/GJgCw8U.png)

**Privelege Escalation:**

Along with the user.txt file a file named contract.txt inside of which we find information about a contract with Skytrain Inc.
![](https://i.imgur.com/DuRDGcW.png)

We start searching on the project folder and we find a python script.
![](https://i.imgur.com/p8MPDQC.png)

The script asks for a parameter and if all the conditions are met the ticket is validated. We make our own .md file to fulfill all the parameters and run the script.
![](https://i.imgur.com/TELnu1m.png)

![](https://i.imgur.com/Boy48kT.png)
This gets us the root flag!























