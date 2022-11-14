# Cracking hashes

* **john -** debe crear un txt con los hashes y luego crackear con john:

{% code overflow="wrap" %}
```bash
john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt

# Con un formato de hash específico
john --format=raw-sha512 hashSha512.txt --wordlist=/usr/share/wordlists/rockyou.txt   
```
{% endcode %}

* **hydra: puede utilizarse sin redirect y sin cookie; pero atención: si redirije debe utilizarse o el programa buscará verificar el mensaje de error en un lugar erróneo, y no en el debido.**

{% code overflow="wrap" %}
```bash
# Genérico
hydra -l <username> -P /usr/share/wordlists/rockyou.txt <website_or_ip> http-post-form "/[redirectionURL]:username=^USER^&password=^PASS^:F=Your username or password is incorrect."

# Ejemplo plantilla incluyendo redirección que hace el submit:
hydra -l admin -P /usr/share/wordlists/rockyou.txt <website_or_ip> http-post-form "/blog/wp-admin/:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=<redirect_website>%2Fblog%2Fwp-admin%2F&testcookie=1:Unknown username. Check again or try your email address.:H=Cookie: wordpress_test_cookie=WP+Cookie+check" -V

# Otros servicios
hydra -l mitch -P /usr/share/wordlists/rockyou.txt <IP> -s 2222 ssh
hidra -l molly -P /usr/share/wordlists/rockyou.txt <IP> ssh -t 4
hydra -l mitch -P /usr/share/wordlists/rockyou.txt <IP> -s 21 ftp

# Ver comandos de módulos para http-form-post
hydra -U http-post-form
Syntax:   <url>:<form parameters>:<condition string>[:<optional>[:<optional>]
```
{% endcode %}

*   _Sintax_

    * First is the page on the server to GET or POST to (URL).
    * Second is the POST/GET variables (taken from either the browser, proxy, etc. with url-encoded (resp. base64-encoded) usernames and passwords being replaced in the "^USER^" (resp. "^USER64^") and "^PASS^" (resp. "^PASS64^") placeholders (FORM PARAMETERS)
    * Third is the string that it checks for an _invalid_ login (by default) Invalid condition login check can be preceded by "F=", successful condition login check must be preceded by "S=". This is where most people get it wrong. You have to check the webapp what a failed string looks like and put it in this parameter!

    The following parameters are optional: (c|C)=/page/uri to define a different page to gather initial cookies from (g|G)= skip pre-requests - only use this when no pre-cookies are required (h|H)=My-Hdr\\: foo to send a user defined HTTP header with each request ^USER\[64]^ and ^PASS\[64]^ can also be put into these headers!

    Note: 'h' will add the user-defined header at the end regardless it's already being sent by Hydra or not. 'H' will replace the value of that header if it exists, by the one supplied by the user, or add the header at the end

    Note that if you are going to put colons (:) in your headers you should escape them with a backslash (\\).

    All colons that are not option separators should be escaped (see the examples above and below). You can specify a header without escaping the colons, but that way you will not be able to put colons in the header value itself, as they will be interpreted by hydra as option separators.

    <pre data-overflow="wrap"><code><strong>#Examples:
    </strong>"/login.php:user=^USER^&#x26;pass=^PASS^:incorrect"
    "/login.php:user=^USER64^&#x26;pass=^PASS64^&#x26;colon=colon\\:escape:S=authlog=.*success"
    "/login.php:user=^USER^&#x26;pass=^PASS^&#x26;mid=123:authlog=.*failed"
    "/:user=^USER&#x26;pass=^PASS^:failed:H=Authorization\\: Basic dT1w:H=Cookie\\: sessid=aaaa:h=X-User\\: ^USER^:H=User-Agent\\: wget"
    "/exchweb/bin/auth/owaauth.dll:destination=http%3A%2F%2F&#x3C;target>%2Fexchange&#x26;flags=0&#x26;username=&#x3C;domain>%5C^USER^&#x26;password=^PASS^&#x26;SubmitCreds=x&#x26;trusted=0:reason=:C=/exchweb"</code></pre>

    The syntax for running hydra to brute force a login form is as follow:

    {% code overflow="wrap" %}
    ```
    hydra -L USERFILE -P PASSWORDFILE DOMAIN/IP METHOD "REDIRECTIONURL:PARAMETERS:FAILMESSAGE:H=COOKIES"
    ```
    {% endcode %}

    Here is what each element means:

    * USERFILE : The wordlist for candidate usernames.
    * PASSWORDFILE: The wordlist for candidate passwords.
    * DOMAIN/IP: The domain name or the IP address of the target web application. For our case, that’s “localhost”.
    * METHOD: Since we have a GET method, we’ll use here http-get-form. If we had POST, we would have used http-post-form.
    * REDIRECTIONURL: This is the complete url to where the form redirects us after pressing submit. So, for our case, we’ll use ‘/DVWA/vulnerabilities/brute/’.
    * PARAMETERS: This is where we will type in our parameters. This will equal the following: “username=^USER^\&password=^PASS^\&Login=Login”, where ^USER^ and ^PASS^ are placeholders for the values in the username and password wordlists respectively.
    * FAILMESSAGE: This is the error message that the web application shows when we type in incorrect credentials. This is how Hydra will know that it got the correct username and password. To get this message, we can simply fill in the form with incorrect credentials. When we try to do this on our target, we get the following message:

    ![https://miro.medium.com/max/874/0\*Hw25yuZvPd6YAr4M.png](https://miro.medium.com/max/874/0\*Hw25yuZvPd6YAr4M.png)

    * COOKIES: This value is not always necessary, but sometimes it can be required. For example, if you need to be authenticated to access your target page, then you need to provide Hydra with the cookies related to your authenticated session. Otherwise, it wouldn’t be able to access the page. So to retrieve the value to use for the cookie, you should right-click on the web page and select Inspect. On the developer tools that appear, select the storage option, and then click on cookies (This method may differ depending on your web browser). By following this process, here is what I got:

    ![https://miro.medium.com/max/1400/0\*HAswpaKhBPDLUSdx.png](https://miro.medium.com/max/1400/0\*HAswpaKhBPDLUSdx.png)

    #### **Launching the Attack**

    Now, our command should look something like this :

    ![https://miro.medium.com/max/1400/0\*aM5B6UW\_rX6cBFoY.png](https://miro.medium.com/max/1400/0\*aM5B6UW\_rX6cBFoY.png)

    I have added the -V flag at the end of the command for verbose output. This tells Hydra to show on the screen, not only the found credentials but also all the failed attempts.

    When we launch our command, we should see Hydra trying a combination of usernames and passwords.

    After a few attempts, Hydra finds the correct username and password.

    ![https://miro.medium.com/max/1400/0\*ukTmeOsPgbbmNg4O.png](https://miro.medium.com/max/1400/0\*ukTmeOsPgbbmNg4O.png)

    As this post demonstrates, we were successful here because the username and password were ridiculously simple. If there was enough complexity, it would have taken us a lot more time to find the correct credentials.
* **hashcat**

{% code overflow="wrap" %}
```bash
sudo hashcat -m 3200 -a0 -S -force [the path to hash] [path to rockyou.txt]

hashcat -m 1710 '6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05' --force /usr/share/wordlists/rockyou.txt
```
{% endcode %}

* **Unshadow**

Sirve para crear un archivo que John the Ripper pueda descifrar. Para lograr esto, _unshadow_ necesita los archivos `/etc/shadow`y `/etc/passwd`

{% code overflow="wrap" %}
```bash
unshadow passwd.txt shadow.txt > passwords.txt   
#Crea un txt con ambos un txt, de passwd y shadow, para crackear con john
```
{% endcode %}
