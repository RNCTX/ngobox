# Ngobox
An ansible deployment for common NGO/nonprofit tools


I co-founded a disaster relief charity in 2017 after hurricane Harvey hit Houston, and it struck me at the time that there was
no ready-made set of common tools available that a non-profit might deploy for basic software functionality.  Invariably people in
similar situations wind up using an assortment of cloud services that don't talk to each other and end up costing them too much money.

Then they either pay a service like Zapier to allow them to 'glue' their cloud services to each other, or do a lot of copying and pasting 
instead of spending their donation money on software.  These scripts set up identical (or better) open source services on your own server 
using your own database, so not only do you keep all of your data secure, but you'll save money if you're paying for things like Slack, 
Mailchimp, Trello, website hosting, etc.


The scripts in this repository will automatically deploy the following on any Ubuntu Linux server:

1) <a href="https://nextcloud.com" target="_blank">Nextcloud</a> for collaboration/goupware/chat/conferencing
2) <a href="https://nodered.org/" target="_blank">Node-RED</a> for quickly building web apis, connecting services, etc
3) <a href="https://couchdb.apache.org/" target="_blank">CouchDB</a> for a 'quick and dirty' data store
4) <a href="https://www.phplist.com/" target="_blank">phpList</a> for mass email capability and volunteer signups
5) <a href="https://www.collaboraoffice.com/code/" target="_blank">Collabora and its Nextcloud plugin</a> for real-time document collaboration
6) <a href="https://github.com/RSS-Bridge/rss-bridge" target="_blank">RSS-Bridge</a> for scraping data from common sources like social media
7) <a href="https://www.nginx.com/" target="_blank">Nginx</a> webserver to serve all of the above
8) <a href="https://mariadb.org/" target="_blank">MariaDB</a> for any SQL database needs (phpList and Nextcloud installed in it automatically)
9) <a href="https://redis.io/" target="_blank">Redis</a> cache for the services above that need one
10) <a href="https://web.archive.org/web/20200511093541/http://manpages.ubuntu.com/manpages/bionic/man1/apticron.1.html" target="_blank">Apticron</a> to email-remind the server owner of updates/upgrades
11) (Optionally) <a href="https://launchpad.net/ufw" target="_blank">UFW firewall</a> for a little extra security

Passwords, databases, permissions, systemd services, server users/groups, and all of those sort of things are set up automatically, and
tuned to reasonable defaults by calculating your server's available memory and number of processors. When ansible finishes, all you have
to do is click a link on the phpList admin page to copy its database tables.  I'll try to automate that with a future update... the link 
is generated dynamically so will probably have to dig into the phpList API to get at it with cURL.

# Screenshots

![img](https://i.imgur.com/2vaDsOn.png)

![img](https://i.imgur.com/gs7qzC2.png)

![img](https://i.imgur.com/Gf3MyGB.png)

![img](https://i.imgur.com/AatGmKh.png)

# Great, what do I need to use this?

**Short answer:** 

AWS API keys with access given to fully manage SES, a dedicated email account to catch mail bounces, and another email account to send notifications to the server admin. A server. A subdomain that isn't your main website. Make something like admin.yoursite.com or backend.yoursite.com. These are internal staff-only tools, not meant to be linked to the public from your main website.

The ansible playbooks presume you log in as the user "ubuntu" via public key auth and don't need a password for sudo (default on AWS EC2 instances).  If you need to change the user or provide a password you can make some <a href="https://github.com/RNCTX/ngobox/wiki/Installing-if-you-need-a-password-for-ssh,-sudo" target="_blank">small changes</a> to the main playbook file to accommodate other setups.

**Longer answer if you're starting from scratch:**

If you don't know what anything in the short answer is, read instructions for setting up a new AWS SES account <a href="https://github.com/RNCTX/ngobox/wiki/AWS-SES-setup" target="_blank">here</a>.

# Instructions

1) Download and unzip the zip or clone the repo on your local machine
2) Install ansible (see <a href="https://github.com/RNCTX/ngobox/wiki/Installing-Ansible,-options-for-Windows-users" target="_blank">here</a> if you don't know what that is)
3) Open a terminal
4) cd to/the/folder you unzipped to
5) type "sh ngobox" without the quotes, and fill in the blanks

When it finishes, go to https://your.domain.com/ and you should be able to log in. All of the web interfaces (nextcloud, couchdb admin, nodered, phpList admin, and rssbridge) have a username of `admin` and the same randomly generated password, listed in the `ansible_default-admin-password-for_admin.txt` file in your computer's (not the server's) home folder. You'll want to log into phpList (in the menu at the top of the nextcloud page) right away since it will require you to click a link and set the admin password to complete its installation.

If there are red notifications in your terminal, feel free to open an issue and paste the red error, I'll try to help you fix it.


# Where should I rent a server from?

Personally for bare-metal type things such as this, I think Digital Ocean and Linode are a better deal than AWS on the low end of the spectrum, plus 
they don't charge for DNS. AWS caps cheapo servers at very low performance limits, whereas Digital Ocean and Linode give you a lot more for less than 20 dollars per month. 
If I were doing all of this on brand new accounts, I'd have an AWS account just for SES to send email with, and do the rest on one of those other two services.

# I'm paying someones else to host Wordpress.  Can I cancel it and move it on this server?

Sure, why not?  Everything you need (Redis, MySQL, Nginx) is already here!

# What sort of things can I do with Node-RED and CouchDB?

<a href="https://youtu.be/X5PuwbIEnUs" target="_blank">Here's</a> an example, build a UPC scanning API in an hour to keep inventory with. Or, 
<a href="https://youtu.be/CVVR8fgV_IA" target="_blank">here's</a> a social media-scraping example.

Humanitarian work isn't like other software work.  Usually you have a budget, design meetings, collaborative development over a period of time, etc. When you're
building something for a non-profit org during a hurricane's aftermath, you have an hour on average to do the most with whatever tools are available for zero money.  
Sadly that answer is all to often "make another spreadsheet."  

But that's not a good answer. People didn't sign up to type things in spreadsheets, they signed up to help people. Data gets lost, 
or poorly entered, and at the end of the week you know you did a lot of work but can't really prove any of it. Humanitarian orgs need better systems for these 
things, and there are open source options out there, it's just a matter of putting all those tools together, so that's what I'm working on.

The natural progression of this set of tools for gathering data is how to present that data, so next up I'm going to work with integrating Vuejs projects into the mix, so that
people can (hopefully) output spiffy Electron and phone apps to consume the data/APIs they are able to create with Node-RED, also in a "low-code" way that a competent sysadmin 
volunteering for your org can stitch together in an hour or two.

# Can you help me do XYZ not covered here?

Sure, <a href="mailto:admin@robertnclayton.net">email me</a> if you want to talk about custom work, cloud service infrastructure help, etc. I've been looking after Unix/Linux servers
for about 20 years now, and am happy to do one-off projects on a per-day basis. 

Cloud infrastructure services are great, usually, but *software* services are not great, usually.  You 
can probably save some money by learning to be a little more critical in evaluating the software you pay for...
