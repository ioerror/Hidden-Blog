#Running a Blog with blosxom as a Tor hidden service
##Tor + Blosxom = love

![](https://github.com/ioerror/Hidden-Blog/raw/master/images/blosxomandtor.png)

Blosxom is a very little blogging application. Being so small
and simple it can be easily tweaked to work well as a Tor Hidden
service. By well we mean that it must protect the server and client
from identity leaks. This is a guide that configures blosxom to run
in a dynamic mode and later it will be updated to run in a static mode.

##Example Tor Hidden Service blog
A blog with this configuration is running in the wild and may be viewed as a
<a href="http://cxoz72fgevhfgitm.onion/">Tor Hidden Service</a> if you have tor
installed or by <a href="https://cxoz72fgevhfgitm.tor2web.org/">using tor2web
if you do not</a>.

##Beat NAT and disregard fascist censorship at the same time!
This setup does not require a public IP address for the system running the
blogging software. It does not require a domain name. Users that have Tor
running on their system will reach the blog without having to worry about DNS
censorship or other kinds of <a
href="https://en.wikipedia.org/wiki/Traffic_analysis">traffic analysis</a>.

##Debian suggestions

Install the following package on Debian:

     apt-get install apache2-mpm-worker libapache2-mod-perl2

Make the directories for the content:

     cp hs-blog-dynamic.apache.conf /etc/apache2/sites-available/
     mkdir -p /var/www/hs-blog/blosxom-entries
     mkdir -p /var/www/hs-blog/blosxom-static
     mkdir -p /var/www/hs-blog/blosxom-dynamic
     cp blosxomandtor.png /var/www/hs-blog/blosxom-static/

##Configuring tor as a hidden service</h3>
The first step is configuring Hidden Serivice and getting apache up (for a 
detailed guide on how to configure a hidden service 
<a href="http://www.torproject.org/docs/tor-hidden-service.html.en">See here</a>)

Append the hidden service configuration data to your Tor configuation file and
reload Tor:

     cat configs/hs-torrc >> /etc/tor/torrc
     /etc/init.d/tor reload

Place the Apache2 configuration file into the Apache2 available-sites
directory:

     mv hs-blog-dynamic.apache.conf /etc/apache2/sites-available/

Enable it:

     a2ensite hs-blog-dynamic.apache.conf
     apachectl reload

##Setting up Blosxom

Place the src/blosxom.cgi into your /var/www/hs-blog/blosxom-dynamic directory:

     mv src/blosxom.cgi /var/www/hs-blog/blosxom-dynamic/
     chown root:www-data /var/www/hs-blog/blosxom-dynamic/blosxom.cgi
     chmod 755 /var/www/hs-blog/blosxom-dynamic/blosxom.cgi

Place the images in the document root:

     mv images/blosxomandtor.png /var/www/hs-blog/blosxom-dynamic/
     mv images/favicon.ico /var/www/hs-blog/blosxom-dynamic/
     chown root:root /var/www/hs-blog/blosxom-dynamic/blosxomandtor.png
     chown root:root /var/www/hs-blog/blosxom-dynamic/favicon.ico
     chmod 755 /var/www/hs-blog/blosxom-dynamic/blosxomandtor.png
     chmod 755 /var/www/hs-blog/blosxom-dynamic/favicon.ico

It should look like the following on the file system:

     -rwxr-xr-x 1 root www-data  17K Dec 22 00:40 blosxom.cgi

If you are running dynamically, I suggest you edit blosxom.cgi and change the
the $url value to the following:

      $url = "/blosxom.cgi";

##Editing your first entry:
Put your blog entries in /var/www/hs-blog/blosxom-entries/ and ensure that the
file names end with .txt or blosxom will not discover them.

##Static rather than dynamic
The first step to running a static blog is to ensure you have a place to put
the data. We suggest /var/www/hs-blog/blosxom-static/ as a good place.

     mdkir -p /var/www/hs-blog/blosxom-static/
     chown root:your-user /var/www/hs-blog/blosxom-static/
     chmod 775 /var/www/hs-blog/blosxom-static/

Place the images in the static document root:

     mv images/blosxomandtor.png /var/www/hs-blog/blosxom-dynamic/
     mv images/favicon.ico /var/www/hs-blog/blosxom-dynamic/
     chown root:root /var/www/hs-blog/blosxom-dynamic/blosxomandtor.png
     chown root:root /var/www/hs-blog/blosxom-dynamic/favicon.ico
     chmod 755 /var/www/hs-blog/blosxom-dynamic/blosxomandtor.png
     chmod 755 /var/www/hs-blog/blosxom-dynamic/favicon.ico

To run Blosxom statically, use the hs-blog-static.apache.conf in place of the
hs-blog-dynamic.apache.conf configuration file. You will need to edit
/var/www/hs-blog/blosxom-dynamic/blosxom.cgi to include a password and then you
need to run:

     /var/www/hs-blog/blosxom-dynamic/blosxom.cgi -password='YOURPASSWORDGOESHERE'

##Tracking changes with git

Check the entire /var/www/hs-blog/ directory into git:

     cd /var/www/hs-blog/
     git init
     git commit -a -m 'blog setup'

After adding blog entries, add each one to git:

     git commit -a -m 'new blog entry'

