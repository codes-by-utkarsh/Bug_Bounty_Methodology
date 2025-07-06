Bug Hunting Methodology : 

Bug hunting is an intricate process that requires persistence, knowledge, and the right tools. This guide outlines a detailed methodology for discovering vulnerabilities, supplemented by useful tools and commands to streamline the process. Let’s dive in!

*RECONNAISSANCE*

1. Setup and Initialization
  Create and Navigate to a Directory
    mkdir example
    cd example
2. Subdomain Enumeration
    2.1 Using Subfinder
      subfinder -d example.com -all -recursive > subfinder_subs.txt
      cat subdomain1.txt | wc -l
    2.2 Using crt.sh
        Using jq with crt.sh
          Tool Installation:
              sudo apt install -y jq
              Command: curl -s "https://crt.sh/?q=%.example.com&output=json" | jq -r '.[].name_value' | sed 's/\*\.//g' | sort -u > crtsh_subs.txt
    2.3 GitHub Search
          Command: github-subdomains -d target.com -t YOUR_GITHUB_TOKEN -o github_subs.txt
    2.4 FFuF
          Command : ffuf -u https://FUZZ.target.com -w wordlist.txt -t 50 -mc 200,403 -o ffuf_subs.txt

Combining Results : cat *_subs.txt | sort -u | anew all_subs.txt

3. Subdomain Status Check
      Using httpx-toolkit
     Command: cat subdomains.txt | httpx-toolkit -ports 80,443,8080,8000,8888 -threads 200 > subdomains_alive.txt
              cat subdomains_alive.txt | wc -l
4. Subdomain Takeover
   subzy : subzy run -dL sub.txt / --targets sub.txt
	 Nuclei : nuclei -l domains.txt -t /root/local/nuclei-template/ -stats -v

5. URL Discovery
   Using Katana
   Command : katana -u subdomains_alive.txt -d 5 -kf -jc -fx -ef woff,pdf,css,png,svg,jpg,woff2,jpeg,gif,svg -o allurls.txt

6. Search for Sensitive Files
   Commmand : cat allurls.txt | grep -E "\.txt|\.log|\.cache|\.secret|\.db|\.backup|\.yml|\.json|\.gz|\.rar|\.zip|\.config"

7.Directory Bruteforcing
    Using Different Tools
    Command : ffuf : ffuf -u https://example.com/FUZZ -w wordlist.txt -H "User-Agent: " -r 
	          dirsearch : dirsearch -l subdomains.txt -e                                 conf,config,bak,backup,swp,old,db,sql,asp,aspx,aspx~,asp~,py,py~,rb,rb~,php,php~,bak,bkp,cache,cgi,conf,csv,html,inc,jar,js,json,jsp,jsp~,lock,log,rar,old,sql,sql.gz,http://sql.zip,sql.tar.gz,sql~,swp,swp~,tar,tar.bz2,tar.gz,txt,wadl,zip,.log,.xml,.js.,.json
	          NOTE : DONT FORGET TO DIRECTORY BRUTEFORCE ON FORBIDDEN PAGES AND 404 NOT FOUND PAGES. THEY WILL ASLO REVEAL SOMETHING.
8. CORS Misconfiguration
    Using Corsy
    Command : python3 corsy.py -i subdomains_alive.txt -t 10 --headers "User-Agent: GoogleBot\nCookie: SESSION=Hacked"

8. Identify SQL Injection Points
   Command : cat allurls.txt | grep -E ".php|.asp|.aspx|.jspx|.jsp" | grep '=' | sed 's/=.*$/=/' | sort | uniq > bsqli.txt
