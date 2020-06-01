<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>How To Set Up a Highly Available PostgreSQL Cluster</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><!--&#10;&#10;This is an article template you can use as a quick starting point when writing DigitalOcean tutorials. Once you've reviewed the template, delete the comments and begin writing your outline or article. You'll find some examples of our custom Markdown at the very bottom of the template.&#10;&#10;As you write, refer to our style and formatting guidelines for more detailed explanations:&#10;&#10;- [do.co/style](https://do.co/style)&#10;&#10;Use our [Markdown previewer](https://www.digitalocean.com/community/markdown) to review your article's formatting.&#10;&#10;Readers should be able to follow your tutorial from the beginning to the end on a DigitalOcean Droplet. Before submitting your article to the editorial team, please be sure to create a new Droplet and test your article from start to finish on it exactly as written. Cut and paste commands from the article into your terminal to make sure there aren't typos in the commands. If you find yourself executing a command that isn't in the article, incorporate it into the article to make sure the reader gets the exact same results. We will test your article and send it back to you if we run into technical problems, which significantly slows down the publication process.&#10;&#10;-->
<h1 id="how-to-set-up-a-highly-available-postgresql-cluster-on-ubuntu-using-patroni">How To Set Up a Highly Available PostgreSQL Cluster on Ubuntu Using Patroni</h1>
<!-- Use Title Case for all Titles -->
<!-- Learn about the title, introduction, and Goals sections at https://do.co/style#title-introduction-and-goals -->
<!-- Learn about formatting headers at https://do.co/style#headers -->
<h3 id="introduction">Introduction</h3>
<!-- Our articles have a specific structure. Learn more at https://do.co/style/structure -->
<p>PostgreSQL is an opensource relational database that can run on major operating systems. It is highly robust and verstaile, but doesn’t have features for the high availability.</p>
<p>In this tutorial, you’ll set up the PostgreSQL with high availability using Patrion and HAProxy.</p>
<p>When you’re finished, you would have set up the PostgreSQL with high availability using Patroni.</p>
<h2 id="prerequisites">Prerequisites</h2>
<!-- Prerequisites let you leverage existing tutorials so you don't have to repeat installation or setup steps in your tutorial. Learn more at https://do.co/style#prerequisites -->
<p>Before you begin this guide you’ll need the following:</p>
<p>-Five Ubuntu 20.04 server droplets set up by following <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">the Ubuntu 20.04 initial server setup guide</a>, including a non-root <code>sudo</code>-enabled user and a firewall.</p>
<p>-Install PostgreSQL in three droplets by following <a href="https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart">the Install PostgreSQL on Ubuntu 20.04</a></p>
<!-- Example:&#10;&#10;* One Ubuntu 18.04 server with at least 1GB of RAM set up by following [the Ubuntu 18.04 initial server setup guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04), including a sudo non-root user and a firewall.&#10;&#10;* Nginx installed on your server, as shown in [How To Install Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04).&#10;&#10;* A domain name configured to point to your server. You can learn how to point domains to DigitalOcean Droplets by following the [How To Set Up a Host Name with DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean) tutorial.&#10;&#10;-->
<h2 id="step-1-—-stopping-postgres-service">Step 1 — Stopping Postgres Service</h2>
<p>When PostgreSQL in installed, it automatically starts as a system service. You should stop this PostgreSQL service so that Patroni can take care of running the PostgreSQL Service.</p>
<p>Stop the PostgreSQL  service using the following command</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo systemctl stop postgresql
</code></pre>
<p>Now the PostgreSQL service is stopped.</p>
<p>You can install Patroni and it can take charge of the PostgreSQL Service.</p>
<h2 id="step-2-—-installing-patroni">Step 2 — Installing Patroni</h2>
<!-- For more information on steps, see https://do.co/style/#steps -->
<p>Introduction to the step. What are we going to do and why are we doing it?</p>
<p>First…</p>
<p>Next…</p>
<p>Finally…</p>
<!--&#10;&#10;If showing a command, explain the command first by talking about what it does. Then show the command.&#10;&#10;If showing a configuration file, try to show only the relevant parts and explain what needs to change.&#10;&#10;-->
<p>Now transition to the next step by telling the reader what’s next.</p>
<h2 id="step-3-—-installing-etcd">Step 3 — Installing ETCD</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="step-4-—-installing-haproxy">Step 4 — Installing HAProxy</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="conclusion">Conclusion</h2>
<p>In this article you have configured Postgres database with High availability. Now you can…</p>
<!-- Speak to reader benefits of this technique or procedure and optionally provide places for further exploration. -->
<!-- Some examples of how to mark up various things&#10;&#10;This is _italics_ and this is **bold**.&#10;&#10;Only use italics and bold for specific things. Learn more at https://do.co/style#bold-and-italics&#10;&#10;This is `inline code`. Use it for referencing package names and commands.&#10;&#10;Here's a command someone types in the Terminal:&#10;&#10;```command&#10;&#10;sudo nano /etc/nginx/sites-available/default&#10;&#10;```&#10;&#10;Here's a configuration file. The label on the first line lets you clearly state the file that's being shown or modified:&#10;&#10;```nginx&#10;&#10;[label /etc/nginx/sites-available/default]&#10;&#10;server {&#10;&#10;listen 80 default_server;&#10;&#10;listen [::]:80 default_server ipv6only=on;&#10;&#10;root &lt;^&gt;/usr/share/nginx/html&lt;^&gt;;&#10;&#10;index index.html index.htm;&#10;&#10;server_name localhost;&#10;&#10;location / {&#10;&#10;try_files $uri $uri/ =404;&#10;&#10;}&#10;&#10;}&#10;&#10;```&#10;&#10;Here's output from a command:&#10;&#10;```&#10;&#10;[secondary_label Output]&#10;&#10;Could not connect to Redis at 127.0.0.1:6379: Connection refused&#10;&#10;```&#10;&#10;Learn about formatting commands and terminal output at https://do.co/style#code&#10;&#10;Key presses should be written in ALLCAPS with in-line code formatting: `ENTER`.&#10;&#10;Use a plus symbol (+) if keys need to be pressed simultaneously: `CTRL+C`.&#10;&#10;This is a &lt;^&gt;variable&lt;^&gt;.&#10;&#10;This is an `&lt;^&gt;in-line code variable&lt;^&gt;`&#10;&#10;Learn more about how to use variables to highlight important items at https://do.co/style#variables&#10;&#10;Use `&lt;^&gt;your_server_ip&lt;^&gt;` when referencing the IP of the server. Use `111.111.111.111` and `222.222.222.222` if you need other IP addresses in examples.&#10;&#10;Learn more about host names and domains at https://do.co/style#users-hostnames-and-domains&#10;&#10;&lt;$&gt;[note]&#10;&#10;**Note:** This is a note.&#10;&#10;&lt;$&gt;&#10;&#10;&lt;$&gt;[warning]&#10;&#10;**Warning:** This is a warning.&#10;&#10;&lt;$&gt;&#10;&#10;Learn more about notes at https://do.co/style#notes-and-warnings&#10;&#10;Screenshots should be in PNG format and hosted on imgur. Embed them in the article using the following format:&#10;&#10;![Alt text for screen readers](/path/to/img.png)&#10;&#10;Learn more about images at https://do.co/style#images-and-other-assets&#10;&#10;-->
</div>
</body>

</html>
