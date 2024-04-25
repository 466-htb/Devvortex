# Devvortex

IP: `10.10.11.242`

## Nmap Scan
ran `nmap -sV IP`
![nmap results](images/nmap.png)
From the nmap scan we can see that port 22 and port 80 are open which means ssh is available and there is also a website being hosted.

## Devvortex Website
Since port 80 was open I decided to navigate to the website being hosted there and it looked like this
![devvortex home](images/devvortex-home.png)
My first instict was to look for hidden paths such as /admin and /login but those yielded no results.
I then tried to automate this process with a tool called `dirbuster`. This unfortunately yielded no results as well.
So, I then looked into possible subdomains. To check theses I used a tool called `ffuf`. I downloaded a list of [subdomains](https://github.com/rbsec/dnscan/blob/master/subdomains-10000.txt) from github and used ffuf to check all of them.
![ffuf results](images/ffuf-subdomain.png)
FFuF revealed to me that there was a `dev` subdomain.

## Dev Subdomain
Upon navigating to dev subdomain I was greeted with the following page.
![dev image](images/devvortex-dev.png)
I repeated the same process in the previous step where I randomly tried paths and got lucky that there was a `/administrator` path.
![admin page](images/admin-login.png)

## Joomla Admin Login
I first tried some basic username and password pairs like `admin:admin`, `root:root`, `admin:password`. All of these failed. 
My next thought was to brute force the login credentials using a tool called `hydra`. Not only was this slow but it proved ineffective so I needed to rethink my strategy. 
Eventually I noticed in Kali there was a link to something called [Exploit Database](https://www.exploit-db.com/). So, I went there and searched for Joomla and sure enought there were lots of CVEs posted there. As I was looking at the CVEs I saw one with a green checkmark and chose that one to look at.
![exploit db](images/exploit-db-search.png)
Upon clicking on it, I saw that it was a vulenrability that leaked information. So, I thought this might work and copied the links in the exploit it gave which were   
`#{root_url}/api/index.php/v1/users?public=true` and `#{root_url}/api/index.php/v1/config/application?public=true`   
When I replaced the root url with the `dev.devvortex.htb` url and submited it in the search bar I got back the username in the first link and the password in the second.
![username](images/username.png)
![password](images/password.png)

## Reverse Shell
Upon entering the admin portal my first thought was to upload a reverse shell. So, I naviagted to where the files were located and added a line of php code that gave me a reverse shell.
![reverse shell](images/reverse-shell.png)
