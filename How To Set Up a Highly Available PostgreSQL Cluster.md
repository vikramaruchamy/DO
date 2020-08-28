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
<p>When you’re finished, you will have a robust and <a href="https://www.digitalocean.com/community/tutorials/what-is-high-availability">highly available</a> PostgreSQL cluster ready for production use.</p>
<h2 id="prerequisites">Prerequisites</h2>
<!-- Prerequisites let you leverage existing tutorials so you don't have to repeat installation or setup steps in your tutorial. Learn more at https://do.co/style#prerequisites -->
<p>Before you begin this guide, you’ll need the following:</p>
<p>-Five Ubuntu 20.04 server droplets set up by following <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">the Ubuntu 20.04 initial server setup guide</a>, including a non-root <code>sudo</code>-enabled user and a firewall.</p>
<p>-To ensure you’re using the latest packages in the servers, update the installed package database in all the servers using <a href="https://www.digitalocean.com/community/tutorials/how-to-manage-packages-in-ubuntu-and-debian-with-apt-get-apt-cache#how-to-update-the-package-database-with-apt-get">Update the installed packages</a> and upgrade the packages to the latest version by using <a href="https://www.digitalocean.com/community/tutorials/how-to-manage-packages-in-ubuntu-and-debian-with-apt-get-apt-cache#how-to-upgrade-installed-packages-with-apt-get">upgrade packages.</a></p>
<p>-Ensure Python-3 is available in the three droplets where PostgreSQL is installed by following the guide <a href="https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server">Install Python 3 and Set Up a Programming Environment on an Ubuntu 20.04 Server</a></p>
<p>-Install PostgreSQL in three droplets by following <a href="https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart">the Install PostgreSQL on Ubuntu 20.04</a></p>
<p>-Install VIM text editors in all your droplets by following the Installation step in the guide <a href="https://www.digitalocean.com/community/tutorials/installing-and-using-the-vim-text-editor-on-a-cloud-server">Installing and using VIM Text editor</a>. Get yourself familiar with the text editor commands. This will make it easier for your to edit the configuration files using the VIM text editor.</p>
<p>-Reserve two droplets for installing etcd and HAProxy.</p>
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
<p>The  <code>pip3 install</code>  command is used to install additional Python packages.</p>
<p>Execute the following command to install Patroni</p>
<pre><code>$ sudo pip3 install patroni

</code></pre>
<p><strong>Note:</strong>  You should execute this command in all three droplets where PostgreSQL is installed, so that the PostgreSQL configuration can be handled using Patroni.</p>
<p>Now, you can install the etcd to handle the distributed cluster.</p>
<!--&#10;&#10;If showing a command, explain the command first by talking about what it does. Then show the command.&#10;&#10;If showing a configuration file, try to show only the relevant parts and explain what needs to change.&#10;&#10;-->
<!--Now transition to the next step by telling the reader what's next.-->
<h2 id="step-3-—-installing-etcd">Step 3 <strong>—</strong> Installing ETCD</h2>
<p><strong>etcd</strong> is a strongly consistent, distributed key-value store that provides a reliable way to store data that needs to be accessed by a distributed system or cluster of machines. It gracefully handles leader elections during network partitions and can tolerate machine failure, even in the leader node.</p>
<p>The <code>apt-get install</code> command is used to install packages with all the necessary dependencies.</p>
<p>Execute the following command to install etcd in the droplet reserved for <strong>etcd</strong>.</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo apt-get install etcd
</code></pre>
<p>Now, you can install HAProxy that provides high availability.</p>
<h2 id="step-4-—-installing-haproxy">Step 4 <strong>—</strong> Installing HAProxy</h2>
<p>HAProxy is free, open source software that provides a high availability load balancer and proxy server for TCP and HTTP-based applications that spreads requests across multiple servers.</p>
<p>The <code>apt-get install</code> command is used to install packages with all the necessary dependencies.</p>
<p>Execute the following command to install etcd in the droplet reserved for <strong>haproxy</strong>.</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo apt-get install haproxy
</code></pre>
<p>Now all the installations are complete in the relevant servers and you’ll start the configuration of etcd, patroni and haproxy.</p>
<h2 id="step-5-—-configuring-etcd">Step 5 <strong>—</strong> Configuring ETCD</h2>
<p>Etcd is a fault-tolerant, distributed key-value store that is used to store the state of the postgres cluster.</p>
<p>You’ve installed ETCD in the step3. Now, you will configure ETCD to handle the leader elections in the highly available cluster. You need to update the etcd configuration file to store the state of the postgres cluster. Using Patroni, all of the postgres nodes makes use of etcd to keep the postgres cluster up and running.</p>
<p>All the available configuration parameters for etcd is available in the <a href="https://etcd.io/docs/v3.4.0/op-guide/configuration/">official etcd page</a>. However, you ll use the necessary configurations flags to configure highly available cluster with default settings.</p>
<p>During the installation of the ETCD, a default etcd configuration file is created in the location <em>/etc/default/etcd</em>.</p>
<p><code>vim</code> tool is used edit the file. Use  <code>sudo vim</code> to open the file in the edit mode. If you do not use <code>sudo</code>, vim will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre><code>$ sudo vim /etc/default/etcd
</code></pre>
<p>Vim opens the file, press <code>i</code> to enter to the insert mode in VIM editor.</p>
<p>Now the etcd default configuration file is opened where all the parameters are commented. Look for the each of the below parameters, uncomment it and update the settings with the relevant etcd droplet IP address.</p>
<p><strong>ETCD_LISTEN_PEER_URLS</strong></p>
<p>This flag informs the etcd to accept incoming requests from its peers on the specified scheme://IP:port combinations.</p>
<p>Update this parameter with your etcd droplet ip address and it should look like below.</p>
<p>ETCD_LISTEN_PEER_URLS=“http://<code>&lt;^&gt;your_etcd_server_ip&lt;^&gt;</code>:2380”</p>
<p><strong>ETCD_LISTEN_CLIENT_URLS</strong></p>
<p>This flag informs the etcd to accept incoming requests from the clients on the specified scheme://IP:port combinations.</p>
<p>Update this parameter with your etcd droplet ip address and it should look like below.</p>
<p>ETCD_LISTEN_CLIENT_URLS=“<a href="http://localhost:2379">http://localhost:2379</a>,http://<code>&lt;^&gt;your_etcd_server_ip&lt;^&gt;</code>:2379”</p>
<p><strong>ETCD_INITIAL_ADVERTISE_PEER_URLS</strong></p>
<p>This flag specifies the list of this member’s peer URLs to advertise to the rest of the cluster. These addresses are used for communicating etcd data around the cluster.</p>
<p>At least one must be routable to all cluster members. These URLs can contain domain names as well.</p>
<p>Update this parameter with your etcd droplet ip address and it should look like below.</p>
<p>ETCD_INITIAL_ADVERTISE_PEER_URLS=“http://<code>&lt;^&gt;your_etcd_server_ip&lt;^&gt;</code>:2380”</p>
<p><strong>ETCD_INITIAL_CLUSTER</strong></p>
<p>This is the initial cluster configuration for bootstrapping. The key is the value of the <code>--name</code> flag for each node provided.</p>
<p>Update this parameter with your etcd droplet ip address and it should look like below.</p>
<p>ETCD_INITIAL_CLUSTER=“default=http://<code>&lt;^&gt;your_etcd_server_ip&lt;^&gt;</code>:2380,”</p>
<p><strong>ETCD_ADVERTISE_CLIENT_URLS</strong><br>
This flag specifies the list of this member’s client URLs to advertise to the rest of the cluster. These URLs can contain domain names as well.</p>
<p>Update this parameter with your etcd droplet ip address and it should look like below.</p>
<p>ETCD_ADVERTISE_CLIENT_URLS=“http://<code>&lt;^&gt;your_etcd_server_ip&lt;^&gt;</code>:2379”</p>
<p><strong>ETCD_INITIAL_CLUSTER_TOKEN</strong></p>
<p>This flag specifies the Initial cluster token for the etcd cluster during bootstrap.</p>
<p>Update this parameter with a token name and it should look like below. Default token name is “etcd-cluster”.  If you want to use the default name itself, then just uncomment this parameter in the configuration file.</p>
<p>ETCD_INITIAL_CLUSTER_TOKEN=“etcd-cluster”</p>
<p><strong>ETCD_INITIAL_CLUSTER_STATE</strong></p>
<p>This flag is used to denote the Initial cluster state (“new” or “existing”). Set to <code>new</code> for all members present during initial static or DNS bootstrapping.</p>
<p>ETCD_INITIAL_CLUSTER_STATE=“new”</p>
<p>Now, press <code>:wq</code> to save the changes to the file and exit the VIM editor.</p>
<p>Now, you need to restart etcd for the configuration changes to be effective.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services. Use <code>restart</code> with <code>systemctl</code> to restart the system service.</p>
<p>Execute the following command to restart the etcdservice.</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo systemctl restart etcd
</code></pre>
<p>Now etcd is running with your configurations.</p>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the system service.</p>
<p>Execute the following command to check the status of the etcd service.</p>
<pre class=" language-command"><code class="prism  language-command">$ sudo systemctl status etcd
</code></pre>
<p>You will see the below messages if the etcd configuration is successful.</p>
<pre class=" language-etcd"><code class="prism  language-etcd">[label etcd log]
etcd.service - etcd - highly-available key value store
     Loaded: loaded (/lib/systemd/system/etcd.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2020-08-27 14:03:57 UTC; 11h ago
       Docs: https://github.com/coreos/etcd
             man:etcd
   Main PID: 14786 (etcd)
      Tasks: 9 (limit: 1137)
     Memory: 61.2M
     CGroup: /system.slice/etcd.service
             └─14786 /usr/bin/etcd

Aug 27 14:03:57 do-04 etcd[14786]: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 3
Aug 27 14:03:57 do-04 etcd[14786]: 8e9e05c52164694d became leader at term 3
Aug 27 14:03:57 do-04 etcd[14786]: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 3
Aug 27 14:03:57 do-04 etcd[14786]: published {Name:do-04 ClientURLs:[http://157.245.111.222:2379]} to cluster cdf818194e3a8c32
Aug 27 14:03:57 do-04 systemd[1]: Started etcd - highly-available key value store.
Aug 27 14:03:57 do-04 etcd[14786]: ready to serve client requests
Aug 27 14:03:57 do-04 etcd[14786]: serving insecure client requests on 157.245.111.222:2379, this is strongly discouraged!
Aug 27 14:03:57 do-04 etcd[14786]: ready to serve client requests
Aug 27 14:03:57 do-04 etcd[14786]: serving insecure client requests on 127.0.0.1:2379, this is strongly discouraged!
Aug 27 16:59:14 do-04 etcd[14786]: sync duration of 1.165829808s, expected less than 1s
</code></pre>
<h2 id="step-6-—-configuring-patroni">Step 6 <strong>—</strong> Configuring Patroni</h2>
<p>Patroni is a Python package used to handle PostgreSQL configuration. You’ve already installed Patroni in the Step 2.</p>
<p>Now, you will configure Patroni <em>using a YAML file</em> in the <em>/etc/patroni/config.yml</em> to handle the PostgreSQL service. A default YAML file named <a href="http://config.yml.in">config.yml.in</a> is created during the installation of Patroni.</p>
<p>First, you should navigate to the /etc/patroni/ directory to rename the file to <em>config.yml</em>. <code>cd</code> command can be used to navigate to the specified directory.</p>
<p>Execute the following command to navigate to the <em>/etc/</em> directory.</p>
<pre class=" language-command"><code class="prism  language-command">$ cd /etc/
</code></pre>
<p>Now, your current working directory is /etc/patroni/.</p>
<p>Next, you need to rename the default YAML file from <em><a href="http://config.yml.in">config.yml.in</a></em> to <em>config.yml</em>. You can use <code>mv</code> command to rename the file.</p>
<p>Execute the below command to rename the file.</p>
<pre><code>$ sudo mv config.yml.in config.yml
</code></pre>
<p>Now, you need to update the <em>config.yml</em> file with the right configuration.</p>
<p><code>vim</code> tool is used edit the file. Use  <code>sudo vim</code> to open the file in the edit mode. If you do not use <code>sudo</code>, vim will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre><code>$ sudo vim config.yml
</code></pre>
<p>Vim opens the file, press <code>i</code> to enter to the insert mode in VIM editor.</p>
<p>Now, update the <em>listen</em> and <em>connect_address</em> under <em>restapi</em> section.</p>
<p>By default, it has 127.0.0.1 as the IP address. This default IP address needs to be updated with <code>&lt;^&gt;your_server_ip&lt;^&gt;</code> address and Port number should be updated with <em>8008</em>.</p>
<pre class=" language-config"><code class="prism .yml language-config">[label /etc/patroni/config.yml]

restapi:
  listen: 111.111.111.111:8008
  connect_address: 111.111.111.111:8008 
  
postgresql:
  listen: 111.111.111.111:5432
  connect_address: 111.111.111.111:5432
  
</code></pre>
<p>update replicator accounts IP address with the three droplets IPS.<br>
Update ETCH host IDs as well.</p>
<p>Now, press <code>:wq</code> to save the changes to the file and exit the VIM editor.</p>
<p>Configuring Patroni in the first Droplet is complete. You need to follow the same steps in other two droplets where PostgreSQL is installed.</p>
<p>Next, you need to configure the data directory.</p>
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
