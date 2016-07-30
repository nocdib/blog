+++
Categories = ["Bits"]
Description = "Creating a personal Squid proxy"
Tags =  ["Squid", "Proxy", "VPS"]
date = "2015-08-09T09:17:32-04:00"
menu = "main"
title = "Setting Up A Squid Proxy Using Digital Ocean"
+++

With all of the challenges that come with being an American living overseas who has the patience to deal with sites like Google and Wikipedia automatically defaulting to their local versions? Not me. But seriously, I do find it annoying when websites default to their Swedish language versions. I’d also like to be able to watch geographically-specific content such as local American news, Netflix and streaming premium cable programs which aren’t accessible to Internet traffic outside of the United States. I tried using a subscription-based public VPN service but experienced significant lagging while streaming Netflix so I decided that a personal proxy was would be the best option.

<a href="http://www.digitalocean.com/" target="_blank">Digital Ocean</a> is a cloud-based hosting services that allows customer to create low-cost, high-performing virtual private servers (VPS) in under a minute. These servers (called droplets) can then be accessed via SSH from any terminal and operate no differently than any local instance of Linux or FreeBSD. Routing my Internet traffic through a U.S.-based server would allow me to use the Internet as if I was in the United States without physically being there. Squid is a proxy server for routing Internet traffic and that is exactly what I need. There are several tutorials on how to configure Squid in various manners but many are for previous versions and not the current version, 3, so I thought I’d share how I got it to work.

After I created a new droplet based in New York running Ubuntu 14.04 64-bit I installed Squid. All of my set up was done using the root user so I never needed to use the “sudo” command but I’ll include it for those who are not using root.

<pre><code class="nohighlight">
sudo apt-get install squid
</code></pre>

After the installation verify that Squid is running.

<pre><code class="nohighlight">
initctl list | grep squid3
</code></pre>

You should get a response like this (but with a different process id):

<pre><code class="nohighlight">
squid3 start/running, process 3481
</code></pre>

If you don’t see it then manually start Squid and check for the running process again:

<pre><code class="nohighlight">
sudo start squid3
</code></pre>

Now when you verify that Squid is running you should see that it is and the process id associated with it.

A crucial step in securing the proxy is requiring authentication, a username and password, to access it. I learned this the hard way a few years ago when I set up a proxy without securing it and it was hijacked by spammers to send mass emails in less than 48 hours. First, run the following command to get the necessary package:

<pre><code class="nohighlight">
apt-get install apache2-utils
</code></pre>

Create a password file. It’s easiest to create it in same folder as the configuration file. Replace [username] with whatever name you desire.

<pre><code class="nohighlight">
htpasswd -c /etc/squid3/passwd [username]
</code></pre>

When prompted to, enter your desired password. Then run this command to find the path to the authentication program. Copy this path because you will need it in the next step. You may get more than one result but select the path to an application and not any other type of file.

<pre><code class="nohighlight">
dpkg -L squid3 | grep ncsa_auth
</code></pre>

Open the configuration file <b>/etc/squid3/squid.conf</b> and add the following near the top of the file:

<pre><code class="nohighlight">
auth_param basic program [path from previous step] /etc/squid3/passwd
auth_param basic children 5
auth_param basic realm [title for the login window]
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive off
acl ncsa_users proxy_auth REQUIRED
http_access allow ncsa_users
</code></pre>

In a nutshell, those lines tell Squid where to find the valid username/password list and that both values are case-insensitve. Then create a user group where authentication is required and only allow users in that group to access the proxy.

Somewhere in the file should be a line that looks like:

<pre><code class="nohighlight">
http_port [port_number]
</code></pre>

Usually the port number is 3128. You can change this value if you desire but remember it since you will be referencing it.

These next configuration options are optional so you can use whatever you desire and skip the steps you do not need since your configuration desires may be different than mine.

<b>**** START OPTIONAL SECTION ****</b>

What I needed from my proxy was for it not to cache, not to show my computer’s IP as the true origin and for all traffic to go through the proxy and not directly to me. The following lines do those things, respectively, and are at the end of the file.

<pre><code class="nohighlight">
cache deny all
request_header_access Allow allow all
request_header_access Authorization allow all
request_header_access WWW-Authenticate allow all
request_header_access Proxy-Authorization allow all
request_header_access Proxy-Authenticate allow all
request_header_access Cache-Control allow all
request_header_access Content-Encoding allow all
request_header_access Content-Length allow all
request_header_access Content-Type allow all
request_header_access Date allow all
request_header_access Expires allow all
request_header_access Host allow all
request_header_access If-Modified-Since allow all
request_header_access Last-Modified allow all
request_header_access Location allow all
request_header_access Pragma allow all
request_header_access Accept allow all
request_header_access Accept-Charset allow all
request_header_access Accept-Encoding allow all
request_header_access Accept-Language allow all
request_header_access Content-Language allow all
request_header_access Mime-Version allow all
request_header_access Retry-After allow all
request_header_access Title allow all
request_header_access Connection allow all
request_header_access Cookie allow all
request_header_access Proxy-Connection allow all
request_header_access User-Agent allow all
request_header_access All deny all
forwarded_for off
always_direct deny all
</code></pre>

<b>**** END OPTIONAL SECTION ****</b>

With all of the options in place you can test that your proxy works. Save the configuration file and apply the changes to Squid with this command:

<pre><code class="nohighlight">
squid3 -k reconfigure
</code></pre>

Change the proxy settings in your browser so that all connections are routed through the public IP of your droplet using the port number that you specified in the configuration file. Everything should work as expected. If you have any questions/issues then feel free to reach me in the comments.
