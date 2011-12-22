#Running a Blog with blosxom as a Tor hidden service
##Tor + Blosxom = love

![](https://github.com/ioerror/Hidden-Blog/raw/master/blosxomandtor.png)

Blosxom is a very little blogging application. Being so small
and simple it can be easily tweaked to work well as a Tor Hidden
service. By well we mean that it must protect the server and client
from identity leaks. This is a guide that configures blosxom to run
in a dynamic mode and later it will be updated to run in a static mode.

##Debian suggestions

Install the following package on Debian:

  apt-get install apache2-mpm-worker libapache2-mod-perl2

Make the directories for the content:

  cp hs-blog.apache.conf /etc/apache2/sites-available/
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

  mv hs-blog /etc/apache2/sites-available/

Enable it:

  a2ensite hs-blog
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

This makes all the links relative, so that they will work with .onion urls.
This also works when using the blog from tor2web. The only issue is when you
try and browse the page from x.tor2web.org/<onion_url>;.  This is not that
important as &lt;onion_url&gt;.tor2web.org works fine, yet I am looking for a
workaround.

##Editing your first entry:
Put your blog entries in /var/www/hs-blog/blosxom-entries/ and ensure that the
file names end with .txt or blosxom will not discover them.

##Tracking changes with git

Check the entire /var/www/hs-blog/ directory into git:

  cd /var/www/hs-blog/
  git init
  git commit -a -m 'blog setup'

After adding blog entries, add each one to git:

  git commit -a -m 'new blog entry'

##Future work
In the future, we believe it would make sense to run Blosxom as <a
href="http://www.blosxom.com/documentation/users/configure/static.html"> an
entirely static blog</a>.
