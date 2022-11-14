# Port & Services

NMAP ports & services scan

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"># Scanning ports and services with versions, OS, Scripts (Versions requires full TCP handshake and is incompatible with -sS Stealth, but -O and -sC are)

# It is best to use intense scanning only on open ports found
nmap -p &#x3C;22,80,135,443...> -sS -T4 -Pn -n -A -vv &#x3C;IP> -oN &#x3C;save.file>
nmap -p &#x3C;22,80,135,443...> -sS -T4 -Pn -n -sVC -O -vv &#x3C;IP> -oN &#x3C;save.file>
# All ports - not recommended due to slowness
nmap -p- -sS -T4 -Pn -n -A -vv &#x3C;IP> -oN &#x3C;save.file>

# Scripts
nmap -p &#x3C;22,80,135,443...> -sS -T4 -Pn -n -sV -sC -vv &#x3C;IP> -oN &#x3C;save.file>
nmap -p &#x3C;22,80,135,443...> -sS -T4 -Pn -n -sV --script vuln -vv &#x3C;IP> -oN &#x3C;save.file>
nmap -p &#x3C;22,80,135,443...> -sS -T4 -Pn -n -sV --script smb* -vv &#x3C;IP> -oN &#x3C;save.file>

-----------------------------------------------------------------
<strong>#UDP scans are necessary for real world audits, but being so slow it is recommended to scan the top ports.
</strong><strong>#In CTF challenges, it is recommended to scan UDP if TCOP does not find anything or if there is a clue that leads to it.
</strong><strong>
</strong>#Nmap "fast" top 1000 UDP ports:
nmap -sU -sV --version-intensity 0 -n -T4 &#x3C;IP> -oN &#x3C;save.file>
#If it found udp, scan with Scripts:
nmap -sU -sV -sC -n -T4 -p {xxxx} &#x3C;IP> -oN &#x3C;save.file.>
</code></pre>
