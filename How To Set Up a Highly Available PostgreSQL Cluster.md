<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DO_Sample_Article</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><!--&#10;&#10;This is an article template you can use as a quick starting point when writing DigitalOcean tutorials. Once you've reviewed the template, delete the comments and begin writing your outline or article. You'll find some examples of our custom Markdown at the very bottom of the template.&#10;&#10;As you write, refer to our style and formatting guidelines for more detailed explanations:&#10;&#10;- [do.co/style](https://do.co/style)&#10;&#10;Use our [Markdown previewer](https://www.digitalocean.com/community/markdown) to review your article's formatting.&#10;&#10;Readers should be able to follow your tutorial from the beginning to the end on a DigitalOcean Droplet. Before submitting your article to the editorial team, please be sure to create a new Droplet and test your article from start to finish on it exactly as written. Cut and paste commands from the article into your terminal to make sure there aren't typos in the commands. If you find yourself executing a command that isn't in the article, incorporate it into the article to make sure the reader gets the exact same results. We will test your article and send it back to you if we run into technical problems, which significantly slows down the publication process.&#10;&#10;-->
<h1 id="how-to-set-up-a-highly-available-postgresql-cluster-on-ubuntu-using-patroni-and-haproxy">How To Set Up a Highly Available PostgreSQL Cluster on Ubuntu Using Patroni and HAProxy</h1>
<!-- Use Title Case for all Titles -->
<!-- Learn about the title, introduction, and Goals sections at https://do.co/style#title-introduction-and-goals -->
<!-- Learn about formatting headers at https://do.co/style#headers -->
<h3 id="introduction">Introduction</h3>
<!-- Our articles have a specific structure. Learn more at https://do.co/style/structure -->
<p>PostgreSQL is an opensource relational database that can run on major operating systems. It is highly robust and versatile, but doesn’t have features for the high availability.</p>
<p>In this tutorial, you’ll set up the PostgreSQL with high availability using Patroni and HAProxy.</p>
<p>When you’re finished, you will have a robust and highly available PostgreSQL cluster ready for production use.</p>
<h2 id="prerequisites">Prerequisites</h2>
<!-- Prerequisites let you leverage existing tutorials so you don't have to repeat installation or setup steps in your tutorial. Learn more at https://do.co/style#prerequisites -->
<p>Before you begin this guide, you’ll need the following:</p>
<p>-Five Ubuntu 20.04 server droplets set up by following <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">the Ubuntu 20.04 initial server setup guide</a>, including a non-root <code>sudo</code>-enabled user and a firewall.</p>
<p>-Install PostgreSQL in three droplets by following <a href="https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart">the Install PostgreSQL on Ubuntu 20.04</a></p>
<p>-Ensure Python-3 is available in the three droplets where PostgreSQL is installed by following the guide <a href="https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server">Install Python 3 and Set Up a Programming Environment on an Ubuntu 20.04 Server</a></p>
<p>-Install VIM text editors in all your droplets by following the Installation step in the guide <a href="https://www.digitalocean.com/community/tutorials/installing-and-using-the-vim-text-editor-on-a-cloud-server">Installing and using VIM Text editor</a>. Get yourself familiar with the text editor commands. This will make it easier for your to edit the configuration files using the VIM text editor.</p>
<p>-Reserve two droplets for installing etcd and HAProxy.</p>
<!-- Example:&#10;&#10;* One Ubuntu 18.04 server with at least 1GB of RAM set up by following [the Ubuntu 18.04 initial server setup guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04), including a sudo non-root user and a firewall.&#10;&#10;* Nginx installed on your server, as shown in [How To Install Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04).&#10;&#10;* A domain name configured to point to your server. You can learn how to point domains to DigitalOcean Droplets by following the [How To Set Up a Host Name with DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean) tutorial.&#10;&#10;-->
<h2 id="step-1-—-stopping-postgres-service">Step 1 <strong>—</strong> Stopping Postgres Service</h2>
<p>When PostgreSQL in installed, it automatically starts as a system service. You should stop this PostgreSQL service so that Patroni can take care of running the PostgreSQL Service.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services. Use <code>stop</code> with <code>systemctl</code> to stop the system service.</p>
<p>Execute the following command to stop the PostgreSQL service.</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo systemctl stop postgresql
</code></pre>
<p><strong>Note:</strong> You should execute this command in all three droplets where PostgreSQL is installed.</p>
<p>Now the PostgreSQL service is stopped in all three droplets.</p>
<p>You can install Patroni, so that it takes charge of running PostgreSQL Service as required.</p>
<h2 id="step-2-—-installing-patroni">Step 2 <strong>—</strong> Installing Patroni</h2>
<!-- For more information on steps, see https://do.co/style/#steps -->
<p>Patroni is a Python package which can be used to manage the PostgreSQL configuration. Patroni is capable of handling Database replication, backup and restoration configurations.</p>
<p>The <code>pip3 install</code> command is used to install additional Python packages.</p>
<p>Execute the following command to install Patroni</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo pip3 install patroni
</code></pre>
<p><strong>Note:</strong> You should execute this command in all three droplets where PostgreSQL is installed, so that the PostgreSQL configuration can be handled using Patroni.</p>
<p>Now, you can configure the Patorni to handle PostgreSQL service running in each droplets.</p>
<!--&#10;&#10;If showing a command, explain the command first by talking about what it does. Then show the command.&#10;&#10;If showing a configuration file, try to show only the relevant parts and explain what needs to change.&#10;&#10;-->
<!--Now transition to the next step by telling the reader what's next.-->
<h2 id="step-3-—-installing-etcd">Step 3 <strong>—</strong> Installing ETCD</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="step-4-—-installing-haproxy">Step 4 <strong>—</strong> Installing HAProxy</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="step-5-—-configuring-patroni">Step 5 <strong>—</strong> Configuring Patroni</h2>
<p>Patroni is a Python package used to handle PostgreSQL configuration. You’ve already installed Patroni in the Step 2.</p>
<p>Now, you will configure Patroni <em>using a YAML file</em> in the <em>/etc/</em> to handle the PostgreSQL service. A default YAML file is available in the offical Patroni GitHub <a href="https://github.com/zalando/patroni/blob/master/postgres0.yml">URL</a>.</p>
<p>First, you should navigate to the /etc/ directory to copy the YAML file to that location. <code>cd</code> command can be used to navigate to the specified directory.</p>
<p>Execute the following command to navigate to the <em>/etc/</em> directory.</p>
<pre class=" language-command"><code class="prism  language-command">$ cd /etc/
</code></pre>
<p>Now, your current working directory is /etc/.</p>
<p>Next, you need to copy the raw <a href="https://raw.githubusercontent.com/zalando/patroni/master/postgres0.yml">default YAML</a> file from GitHub to the /etc/ directory.</p>
<p><code>curl</code> tool is used to copy data from a server to another server.</p>
<p>Execute the below command to copy the YAML file from GitHub to your server.</p>
<pre><code>$ curl -O https://raw.githubusercontent.com/zalando/patroni/master/postgres0.yml
</code></pre>
<p>The -O option in the <code>curl</code> command copies the file with the same name as it is in the source. Here it creates a file named <em>postgres0.yml</em>.</p>
<p>Now, you need to update the <em>postgres0.yml</em> file with the right configuration.</p>
<p><code>vim</code> tool is used edit the file. Use  <code>sudo vim</code> to open the file in the edit mode. If you do not use <code>sudo</code>, vim will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre><code>$ sudo vim postgres0.yml
</code></pre>
<p>Vim opens the file, press <code>i</code> to enter to the insert mode in VIM editor.</p>
<p>Now, update the <em>listen</em> and <em>connect_address</em> under <em>restapi</em> and <em>postgresql sections</em> respectively.</p>
<p>By default, it has 127.0.0.1 as the IP address. This default IP address needs to be updated with <code>&lt;^&gt;your_server_ip&lt;^&gt;</code> address and Port number can be let it as it is:</p>
<pre class=" language-postgres0"><code class="prism .yml language-postgres0">[label /etc/postgres0.yml]

restapi:
  listen: 111.111.111.111:8008
  connect_address: 111.111.111.111:8008 
  
postgresql:
  listen: 111.111.111.111:5432
  connect_address: 111.111.111.111:5432
  
</code></pre>
<p>Now, press <code>:w</code> to save the changes to the file and exit the VIM editor.</p>
<p>Configuring Patroni in the first Droplet is complete. You need to follow the same steps in other two droplets where PostgreSQL is installed.</p>
<p>Next, you need to configure the data directory.</p>
<h2 id="step-6-—-configuring-etcd">Step 6 <strong>—</strong> Configuring ETCD</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="step-7-—-configuring-haproxy">Step 7 <strong>—</strong> Configuring HAProxy</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="step-8-—-testing-the-setup">Step 8 <strong>—</strong> Testing the Setup</h2>
<p>Another introduction</p>
<p>Your content</p>
<p>Transition to the next step.</p>
<h2 id="conclusion">Conclusion</h2>
<p>In this article, you have set up a robust and highly available PostgreSQL cluster.</p>
<p>Now you can improve it further by</p>
<ul>
<li>adding larger etcd cluster to improve availability</li>
<li>adding HAProxy server and configure IP failover to create a highly available HAproxy cluster</li>
</ul>
<!-- Speak to reader benefits of this technique or procedure and optionally provide places for further exploration. -->
<!-- Some examples of how to mark up various things&#10;&#10;This is _italics_ and this is **bold**.&#10;&#10;Only use italics and bold for specific things. Learn more at https://do.co/style#bold-and-italics&#10;&#10;This is `inline code`. Use it for referencing package names and commands.&#10;&#10;Here's a command someone types in the Terminal:&#10;&#10;```command&#10;&#10;sudo nano /etc/nginx/sites-available/default&#10;&#10;```&#10;&#10;Here's a configuration file. The label on the first line lets you clearly state the file that's being shown or modified:&#10;&#10;```nginx&#10;&#10;[label /etc/nginx/sites-available/default]&#10;&#10;server {&#10;&#10;listen 80 default_server;&#10;&#10;listen [::]:80 default_server ipv6only=on;&#10;&#10;root &lt;^&gt;/usr/share/nginx/html&lt;^&gt;;&#10;&#10;index index.html index.htm;&#10;&#10;server_name localhost;&#10;&#10;location / {&#10;&#10;try_files $uri $uri/ =404;&#10;&#10;}&#10;&#10;}&#10;&#10;```&#10;&#10;Here's output from a command:&#10;&#10;```&#10;&#10;[secondary_label Output]&#10;&#10;Could not connect to Redis at 127.0.0.1:6379: Connection refused&#10;&#10;```&#10;&#10;Learn about formatting commands and terminal output at https://do.co/style#code&#10;&#10;Key presses should be written in ALLCAPS with in-line code formatting: `ENTER`.&#10;&#10;Use a plus symbol (+) if keys need to be pressed simultaneously: `CTRL+C`.&#10;&#10;This is a &lt;^&gt;variable&lt;^&gt;.&#10;&#10;This is an `&lt;^&gt;in-line code variable&lt;^&gt;`&#10;&#10;Learn more about how to use variables to highlight important items at https://do.co/style#variables&#10;&#10;Use `&lt;^&gt;your_server_ip&lt;^&gt;` when referencing the IP of the server. Use `111.111.111.111` and `222.222.222.222` if you need other IP addresses in examples.&#10;&#10;Learn more about host names and domains at https://do.co/style#users-hostnames-and-domains&#10;&#10;&lt;$&gt;[note]&#10;&#10;**Note:** This is a note.&#10;&#10;&lt;$&gt;&#10;&#10;&lt;$&gt;[warning]&#10;&#10;**Warning:** This is a warning.&#10;&#10;&lt;$&gt;&#10;&#10;Learn more about notes at https://do.co/style#notes-and-warnings&#10;&#10;Screenshots should be in PNG format and hosted on imgur. Embed them in the article using the following format:&#10;&#10;![Alt text for screen readers](/path/to/img.png)&#10;&#10;Learn more about images at https://do.co/style#images-and-other-assets&#10;&#10;-->
</div>
</body>

</html>
