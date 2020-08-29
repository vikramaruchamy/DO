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
<p>-Install PostgreSQL in three droplets by following <a href="https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart">the Install PostgreSQL on Ubuntu 20.04</a>. These three droplets are referred as node-1, node-2, node-3 in the tutorial.</p>
<p>-Install VIM text editors in all your droplets by following the Installation step in the guide <a href="https://www.digitalocean.com/community/tutorials/installing-and-using-the-vim-text-editor-on-a-cloud-server">Installing and using VIM Text editor</a>. Get yourself familiar with the text editor commands. This will make it easier for your to edit the configuration files using the VIM text editor.</p>
<p>-Reserve two droplets for installing etcd and HAProxy. These droplets are referred as node-4 and node-5 respectively in the tutorial.</p>
<h2 id="step-1-—-stopping-postgres-service">Step 1 <strong>—</strong> Stopping Postgres Service</h2>
<p>When PostgreSQL in installed, it automatically starts as a system service. You should stop this PostgreSQL service so that Patroni can take care of running the PostgreSQL Service.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services. Use <code>stop</code> with <code>systemctl</code> to stop the system service.</p>
<p>Execute the following command to stop the PostgreSQL service.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl stop postgresql
</code></pre>
<p><strong>Note:</strong> You should execute this command in all three droplets where PostgreSQL is installed.</p>
<p>Now the PostgreSQL service is stopped in all nodes(node-1, node-2, node-3).</p>
<p>You can install Patroni, so that it takes charge of running PostgreSQL Service as required.</p>
<h2 id="step-2-—-installing-patroni">Step 2 <strong>—</strong> Installing Patroni</h2>
<!-- For more information on steps, see https://do.co/style/#steps -->
<p>Patroni is a Python package which can be used to manage the PostgreSQL configuration. Patroni is capable of handling Database replication, backup and restoration configurations.</p>
<p>The  <code>pip3 install</code>  command is used to install additional Python packages.</p>
<p>Execute the following command to install Patroni along with its dependencies psycopg2 and python-etcd.</p>
<pre class=" language-command"><code class="prism  language-command">sudo pip3 install patroni psycopg2 python-etcd
</code></pre>
<p>Patroni uses utilities that come installed with postgres, located in the  <strong>/usr/lib/postgresql/12/bin</strong>  directory by default. You need to create symbolic links in the PATH to ensure that Patroni can find the utilities.</p>
<p><code>ln</code> command with <code>-s</code> option is used to create symbolic links. Execute the below command to create the symbolic link.</p>
<pre class=" language-command"><code class="prism  language-command">sudo ln -s /usr/lib/postgresql/&lt;^&gt;12&lt;^&gt;/bin/* /usr/sbin/
</code></pre>
<p>Make sure you replace postgresql version according to the version installed.</p>
<p><strong>Note:</strong>  You should execute this command in all three droplets(node-1, node-2, node-3) where PostgreSQL is installed, so that the PostgreSQL configuration can be handled using Patroni.</p>
<p>Now, you can install the etcd to handle the distributed cluster.</p>
<!--&#10;&#10;If showing a command, explain the command first by talking about what it does. Then show the command.&#10;&#10;If showing a configuration file, try to show only the relevant parts and explain what needs to change.&#10;&#10;-->
<!--Now transition to the next step by telling the reader what's next.-->
<h2 id="step-3-—-installing-etcd">Step 3 <strong>—</strong> Installing ETCD</h2>
<p><strong>etcd</strong> is a strongly consistent, distributed key-value store that provides a reliable way to store data that needs to be accessed by a distributed system or cluster of machines. It gracefully handles leader elections during network partitions and can tolerate machine failure, even in the leader node.</p>
<p>The <code>apt-get install</code> command is used to install packages with all the necessary dependencies.</p>
<p>Execute the following command to install etcd in the node-4 reserved for <strong>etcd</strong>.</p>
<pre class=" language-command"><code class="prism  language-command">sudo apt-get install etcd
</code></pre>
<p>Now, you can install HAProxy that provides high availability.</p>
<h2 id="step-4-—-installing-haproxy">Step 4 <strong>—</strong> Installing HAProxy</h2>
<p>HAProxy is free, open source software that provides a high availability load balancer and proxy server for TCP and HTTP-based applications that spreads requests across multiple servers.</p>
<p>The <code>apt-get install</code> command is used to install packages with all the necessary dependencies.</p>
<p>Execute the following command to install etcd in the node-5 reserved for <strong>haproxy</strong>.</p>
<pre class=" language-command"><code class="prism  language-command">sudo apt-get install haproxy
</code></pre>
<p>Now all the installations are complete in the relevant servers and you’ll start the configuration of etcd, patroni and haproxy.</p>
<h2 id="step-5-—-configuring-etcd">Step 5 <strong>—</strong> Configuring ETCD</h2>
<p>Etcd is a fault-tolerant, distributed key-value store that is used to store the state of the postgres cluster. You’ve installed ETCD in the step3.</p>
<p>Now, you will configure ETCD to handle the leader elections in the highly available cluster and store the state of the postgres cluster. Using Patroni, all of the postgres nodes makes use of etcd to keep the postgres cluster up and running.</p>
<p>However, you ll use the necessary configurations flags to configure highly available cluster with default settings as explained below.</p>
<p>During the installation of the ETCD, a default etcd configuration file is created in the location <em>/etc/default/etcd</em>.</p>
<p><code>vim</code> tool is used edit the file. Use  <code>sudo vim</code> to open the file in the edit mode. If you do not use <code>sudo</code>, vim will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre class=" language-command"><code class="prism  language-command">sudo vim /etc/default/etcd
</code></pre>
<p>Vim opens the file, press <code>i</code> to enter to the insert mode in VIM editor.</p>
<p>Now the <em>etcd</em> default configuration file is opened where all the parameters are commented. Look for the each of the below parameters, uncomment it and update the settings with the relevant etcd droplet IP address as give below.</p>
<pre><code>[label /etc/default/etcd]
ETCD_LISTEN_PEER_URLS="http://`&lt;^&gt;node-4_server_ip&lt;^&gt;`:2380"
ETCD_LISTEN_CLIENT_URLS="http://localhost:2379,http://`&lt;^&gt;node-4_server_ip&lt;^&gt;`:2379"
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://`&lt;^&gt;node-4_server_ip&lt;^&gt;`:2380"
ETCD_INITIAL_CLUSTER="default=http://`&lt;^&gt;node-4_server_ip&lt;^&gt;`:2380,"
ETCD_ADVERTISE_CLIENT_URLS="http://`&lt;^&gt;node-4_server_ip&lt;^&gt;`:2379"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
</code></pre>
<p>Press <code>:wq</code> to save the changes to the file and exit the VIM editor.</p>
<p>You can learn more about etcd configuration parameters in the <a href="https://etcd.io/docs/v3.4.0/op-guide/configuration/">official etcd page</a>. Here’s what each line in this file is for:</p>
<ul>
<li>
<p>ETCD LISTEN PEER URLS  - This flag informs the etcd to accept incoming requests from its peers on the specified scheme://IP:port combinations.</p>
</li>
<li>
<p>ETCD LISTEN CLIENT URLS - This flag informs the etcd to accept incoming requests from the clients on the specified scheme://IP:port combinations.</p>
</li>
<li>
<p>ETCD INITIAL ADVERTISE PEER URLS - This flag specifies the list of this member’s peer URLs to advertise to the rest of the cluster. These addresses are used for communicating etcd data around the cluster.<br>
At least one must be routable to all cluster members. These URLs can contain domain names as well.</p>
</li>
<li>
<p>ETCD INITIAL CLUSTER - This is the initial cluster configuration for bootstrapping. The key is the value of the <code>--name</code> flag for each node provided.</p>
</li>
<li>
<p>ETCD ADVERTISE CLIENT URLS - This flag specifies the list of this member’s client URLs to advertise to the rest of the cluster. These URLs can contain domain names as well.</p>
</li>
<li>
<p>ETCD INITIAL CLUSTER TOKEN - This flag specifies the Initial cluster token for the etcd cluster during bootstrap.  Default token name is “etcd-cluster”.</p>
</li>
<li>
<p>ETCD INITIAL CLUSTER STATE - This flag is used to denote the Initial cluster state (“new” or “existing”). Set to <code>new</code> for all members present during initial static or DNS bootstrapping.</p>
</li>
</ul>
<p>Now, you need to restart etcd for the configuration changes to be effective.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services. Use <code>restart</code> with <code>systemctl</code> to restart the system service.</p>
<p>Execute the following command to restart the etcdservice.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl restart etcd
</code></pre>
<p>Now <em>etcd</em> is running with the updated configurations.</p>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the system service.</p>
<p>Execute the following command to check the status of the etcd service.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl status etcd
</code></pre>
<p>You will see the below messages if the etcd configuration is successful.</p>
<pre><code>[label etcd log]
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
Aug 27 16:59:14 do-04 etcd[14786]: sync duration of 1.165829808s, expected less than 1s
</code></pre>
<h2 id="step-6-—-configuring-patroni">Step 6 <strong>—</strong> Configuring Patroni</h2>
<p>Patroni is a Python package used to handle PostgreSQL configuration. You’ve already installed Patroni in the Step 2.</p>
<p>Now, you will configure Patroni  <em>using a YAML file</em>  in the  <em>/etc/patroni/</em> directory  to handle the PostgreSQL service. A default YAML file is available in the offical Patroni GitHub  <a href="https://github.com/zalando/patroni/blob/master/postgres0.yml">URL</a>.</p>
<p>Create a directory patroni inside the /etc/ folder using the <code>mkdir</code> command as below.</p>
<pre class=" language-command"><code class="prism  language-command">mkdir /etc/patroni
</code></pre>
<p>You should navigate to the /etc/patroni/ directory to copy the YAML file to that location.  <code>cd</code>  command can be used to navigate to the specified directory.</p>
<p>Execute the following command to navigate to the  <em>/etc/patroni/</em>  directory.</p>
<pre class=" language-command"><code class="prism  language-command">cd /etc/patroni/
</code></pre>
<p>Now, your current working directory is /etc/patroni/.</p>
<p>Next, you need to copy the raw  <a href="https://raw.githubusercontent.com/zalando/patroni/master/postgres0.yml">default YAML</a>  file from GitHub to the /etc/patroni/ directory.</p>
<p><code>curl</code>  tool is used to copy data from a server to another server.</p>
<p>Execute the below command to copy the YAML file from GitHub to your server.</p>
<pre class=" language-command"><code class="prism  language-command">curl -o config.yml https://raw.githubusercontent.com/zalando/patroni/master/postgres0.yml
</code></pre>
<p>The -o option in the  <code>curl</code>  command copies the file with the filename specified in the command. Here it creates a file named config.yml.</p>
<p>Now, you need to update the config.yml  file with the right configuration.</p>
<p><code>vim</code>  tool is used edit the file. Use  <code>sudo vim</code>  to open the file in the edit mode. If you do not use  <code>sudo</code>, vim will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre class=" language-command"><code class="prism  language-command">sudo vim config.yml
</code></pre>
<p>Vim opens the file, press  <code>i</code>  to enter to the insert mode in VIM editor.</p>
<p>Update the highlighted parameters in the config.yml.</p>
<pre><code>[label /etc/patroni/config.yml]
scope: &lt;^&gt;postgres&lt;^&gt;
namespace: /service/
name: &lt;^&gt;node1&lt;^&gt;

restapi:
  listen: &lt;^&gt;node-1_server_ip&lt;^&gt;:8008
  connect_address: &lt;^&gt;node-1_server_ip&lt;^&gt;:8008
#  certfile: /etc/ssl/certs/ssl-cert-snakeoil.pem
#  keyfile: /etc/ssl/private/ssl-cert-snakeoil.key
#  authentication:
#    username: username
#    password: password

# ctl:
#   insecure: false # Allow connections to SSL sites without certs
#   certfile: /etc/ssl/certs/ssl-cert-snakeoil.pem
#   cacert: /etc/ssl/certs/ssl-cacert-snakeoil.pem

etcd:
  #Provide host to do the initial discovery of the cluster topology:
  host: &lt;^&gt;node-4_server_ip&lt;^&gt;:2379
  #Or use "hosts" to provide multiple endpoints
  #Could be a comma separated string:
  #hosts: host1:port1,host2:port2
  #or an actual yaml list:
  #hosts:
  #- host1:port1
  #- host2:port2
  #Once discovery is complete Patroni will use the list of advertised clientURLs
  #It is possible to change this behavior through by setting:
  #use_proxies: true

#raft:
#  data_dir: .
#  self_addr: 127.0.0.1:2222
#  partner_addrs:
#  - 127.0.0.1:2223
#  - 127.0.0.1:2224

bootstrap:
  # this section will be written into Etcd:/&lt;namespace&gt;/&lt;scope&gt;/config after initializing new cluster
  # and all other cluster members will use it as a `global configuration`
  dcs:
    ttl: 30
    loop_wait: 10
    retry_timeout: 10
    maximum_lag_on_failover: 1048576
#    master_start_timeout: 300
#    synchronous_mode: false
    #standby_cluster:
      #host: 127.0.0.1
      #port: 1111
      #primary_slot_name: patroni
    postgresql:
      use_pg_rewind: true
#      use_slots: true
      parameters:
#        wal_level: hot_standby
#        hot_standby: "on"
#        wal_keep_segments: 8
#        max_wal_senders: 10
#        max_replication_slots: 10
#        wal_log_hints: "on"
#        archive_mode: "on"
#        archive_timeout: 1800s
#        archive_command: mkdir -p ../wal_archive &amp;&amp; test ! -f ../wal_archive/%f &amp;&amp; cp %p ../wal_archive/%f
#      recovery_conf:
#        restore_command: cp ../wal_archive/%f %p

  # some desired options for 'initdb'
  initdb:  # Note: It needs to be a list (some options need values, others are switches)
  - encoding: UTF8
  - data-checksums

  pg_hba:  # Add following lines to pg_hba.conf after running 'initdb'
  # For kerberos gss based connectivity (discard @.*$)
  #- host replication replicator 127.0.0.1/32 gss include_realm=0
  #- host all all 0.0.0.0/0 gss include_realm=0
  - host replication replicator 127.0.0.1/32 md5
  &lt;^&gt;- host replication replicator node-1_server_ip/0 md5&lt;^&gt;
  &lt;^&gt;- host replication replicator node-2_server_ip/0 md5&lt;^&gt;
  &lt;^&gt;- host replication replicator node-3_server_ip/0 md5&lt;^&gt;
  - host all all 0.0.0.0/0 md5
#  - hostssl all all 0.0.0.0/0 md5

  # Additional script to be launched after initial cluster creation (will be passed the connection URL as parameter)
# post_init: /usr/local/bin/setup_cluster.sh

  # Some additional users users which needs to be created after initializing new cluster
  users:
    admin:
      password: admin
      options:
        - createrole
        - createdb

postgresql:
  listen: &lt;^&gt;node-1_server_ip&lt;^&gt;:5432
  connect_address: &lt;^&gt;node-1_server_ip&lt;^&gt;:5432
  data_dir: &lt;^&gt;/data/patroni&lt;^&gt;
#  bin_dir:
#  config_dir:
  pgpass: /tmp/pgpass0
  authentication:
    replication:
      username: replicator
      password: &lt;^&gt;rep-pass&lt;^&gt;
    superuser:
      username: postgres
      password: &lt;^&gt;zalando&lt;^&gt;
    rewind:  # Has no effect on postgres 10 and lower
      username: rewind_user
      password: rewind_password
  # Server side kerberos spn
#  krbsrvname: postgres
  parameters:
    # Fully qualified kerberos ticket file for the running user
    # same as KRB5CCNAME used by the GSS
#   krb_server_keyfile: /var/spool/keytabs/postgres
    unix_socket_directories: '.'

#watchdog:
#  mode: automatic # Allowed values: off, automatic, required
#  device: /dev/watchdog
#  safety_margin: 5

tags:
    nofailover: false
    noloadbalance: false
    clonefrom: false
    nosync: false
</code></pre>
<p>You can learn more about patroi configuration parameters in the <a href="https://patroni.readthedocs.io/en/latest/SETTINGS.html">official patroni docs page</a>. Here’s what each line in this file is for in the different sections of the file:</p>
<p>Global section:</p>
<ul>
<li>scope - Name for the highly available postgres cluster</li>
<li>namespace -path within the configuration store where Patroni will keep information about the cluster. Default value: “/service”.</li>
<li>name - the name of the host. Must be unique for the cluster. for e.g. (node-1 in the first server, node-2 in the second server and so on).</li>
</ul>
<p>RestAPI section:</p>
<ul>
<li>listen - IP address (or hostname) and port that Patroni will listen to for the REST API - to provide also the same health checks and cluster messaging between the participating nodes.</li>
<li>connect_address - IP address (or hostname) and port, to access the Patroni’s <a href="https://patroni.readthedocs.io/en/latest/rest_api.html#rest-api">REST API</a>.</li>
</ul>
<p>etcd section:</p>
<ul>
<li>host - information for the etcd endpoint for the clusters.</li>
</ul>
<p>Bootstrap configuration section:</p>
<ul>
<li>pg_hba - list of lines that you should add to pg_hba.conf. Client authentication is controlled by this pg_hba.conf file. You can read more about this file in the  <a href="https://www.postgresql.org/docs/9.1/auth-pg-hba-conf.html">official postgresql page</a>.<br>
You will add the lines of the three nodes which can be used for authentication during the replication between the servers.</li>
</ul>
<p>postgresql section:</p>
<ul>
<li>listen - IP address + port that Postgres listens to. It must be accessible from other nodes in the cluster.</li>
<li>connect_address - IP address + port through which Postgres is accessible from other nodes and applications.</li>
<li>data_dir - The location of the Postgres data directory, either <a href="https://patroni.readthedocs.io/en/latest/existing_data.html#existing-data">existing</a> or to be initialized by Patroni.</li>
<li>authentication - Authentication section has the username and passwords specified for the superuser, replication user. Superuser is used by patroni to connect to the postgres and replication user is used by replicas to access master via streaming replication. You can specify the passwords for the superuser and replication user in the password properties of the users respectively.</li>
</ul>
<p>Now, press  <code>:w</code>  to save the changes to the file and exit the VIM editor.</p>
<p>Next, you need to configure the data directory. In the Patroni configuration file, data directory is specified as <em>/data/patroni</em>.</p>
<p>The superuser(<em>postgres</em>) mentioned in the configuration file, should be able to write into the data directory.</p>
<p>Create a directory patroni inside the /data/ folder using the <code>mkdir</code> command as below.</p>
<pre class=" language-command"><code class="prism  language-command">sudo mkdir -p /data/patroni
</code></pre>
<p>Now, make the superuser <em>postgres</em> the owner of /data/patroni.</p>
<pre class=" language-command"><code class="prism  language-command">sudo chown postgres:postgres /data/patroni
</code></pre>
<p>In order to prevent anybody else from changing this directory, change the permissions on this directory to make it accessible only to the <em>postgres</em> user(owner of the directory).</p>
<pre class=" language-command"><code class="prism  language-command">sudo chmod 700 /data/patroni
</code></pre>
<p>Next, you need to create a systemd script that will allow us to start, stop and monitor Patroni.</p>
<p>You need to create a file <em>/etc/systemd/system/patroni.service</em> on all three nodes (node-1, node-2, node-3 ) using the vim command.</p>
<pre class=" language-command"><code class="prism  language-command">sudo vim /etc/systemd/system/patroni.service
</code></pre>
<p>Vim opens the file, press  <code>i</code>  to enter to the insert mode in VIM editor.</p>
<p>Update the file with the below contents.</p>
<pre><code>[label /etc/systemd/system/patroni.service]
[Unit]
Description=High availability PostgreSQL Cluster
After=syslog.target network.target

[Service]
Type=simple
User=postgres
Group=postgres
ExecStart=/usr/local/bin/patroni &lt;^&gt;/etc/patroni/config.yml&lt;^&gt;
KillMode=process
TimeoutSec=30
Restart=no

[Install]
WantedBy=multi-user.targ
</code></pre>
<p>As highlighted above, you need to update the location of the patroni configuration file.  You can learn more about the systemd unit and unit file in <a href="https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files">this</a> tutorial.</p>
<p>Now, you need to start Patroni for handling the postgres database.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services. Use <code>start</code> with <code>systemctl</code> to start the system service.</p>
<p>Execute the following command to start the patroni.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl start patroni
</code></pre>
<p>Now <em>patroni</em> is running and its handling the postgresql database.</p>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the system service.</p>
<p>Execute the following command to check the status of the patroni service.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl status patroni
</code></pre>
<p>You will see the below messages if the Patroni configuration is successful.</p>
<pre><code>[label node-1 patroni logs]
● patroni.service - High availability PostgreSQL Cluster
     Loaded: loaded (/etc/systemd/system/patroni.service; disabled; vendor preset: enabled)
     Active: active (running) since Fri 2020-08-28 00:41:53 UTC; 1 day 3h ago
   Main PID: 3045 (patroni)
      Tasks: 14 (limit: 1137)
     Memory: 76.4M
     CGroup: /system.slice/patroni.service
             ├─3045 /usr/bin/python3 /usr/local/bin/patroni /etc/patroni/config.yml
             ├─3071 postgres -D /data/patroni --config-file=/data/patroni/postgresql.conf --listen_addresses=128.199.30.42 --port=5432 --cluster_name=postgres --wal_level=replica --hot_standby=on --max_connections=100 --max_wal_senders=&gt;
             ├─3076 postgres: postgres: checkpointer
             ├─3077 postgres: postgres: background writer
             ├─3078 postgres: postgres: stats collector
             ├─3083 postgres: postgres: postgres postgres 128.199.30.42(51028) idle
             ├─3183 postgres: postgres: walwriter
             ├─3184 postgres: postgres: autovacuum launcher
             ├─3185 postgres: postgres: logical replication launcher
             └─3191 postgres: postgres: walsender replicator 128.199.30.45(58144) streaming 0/5000550

Aug 29 04:19:41 do-01 patroni[3045]: 2020-08-29 04:19:41,453 INFO: Lock owner: node1; I am node1
Aug 29 04:19:41 do-01 patroni[3045]: 2020-08-29 04:19:41,458 INFO: no action.  i am the leader with the lock
Aug 29 04:19:51 do-01 patroni[3045]: 2020-08-29 04:19:51,453 INFO: Lock owner: node1; I am node1
Aug 29 04:19:51 do-01 patroni[3045]: 2020-08-29 04:19:51,458 INFO: no action.  i am the leader with the lock
Aug 29 04:20:01 do-01 patroni[3045]: 2020-08-29 04:20:01,453 INFO: Lock owner: node1; I am node1
Aug 29 04:20:01 do-01 patroni[3045]: 2020-08-29 04:20:01,459 INFO: no action.  i am the leader with the lock
Aug 29 04:20:11 do-01 patroni[3045]: 2020-08-29 04:20:11,453 INFO: Lock owner: node1; I am node1
Aug 29 04:20:11 do-01 patroni[3045]: 2020-08-29 04:20:11,458 INFO: no action.  i am the leader with the lock
Aug 29 04:20:21 do-01 patroni[3045]: 2020-08-29 04:20:21,453 INFO: Lock owner: node1; I am node1
Aug 29 04:20:21 do-01 patroni[3045]: 2020-08-29 04:20:21,460 INFO: no action.  i am the leader with the lock

</code></pre>
<p>Configuring Patroni in the first Droplet is complete. You need to follow the same steps in other two droplets(node-2 and node-3) where PostgreSQL is installed. Just update the node-2_ip_address and node-3_IP_address wherever applicable.</p>
<p>One you complete the patroni configuration in the node-2 and node-3, these nodes will join in the cluster and follow the leader node-1.</p>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the patroni in node-2.</p>
<p>Execute the following command to check the status of the patroni service.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl status patroni
</code></pre>
<p>You will see the below messages if the node-2 is joined in the cluster successfully.</p>
<pre><code>[label node-2 patroni logs]
● patroni.service - High availability PostgreSQL Cluster
     Loaded: loaded (/etc/systemd/system/patroni.service; disabled; vendor preset: enabled)
     Active: active (running) since Thu 2020-08-27 14:05:32 UTC; 1 day 14h ago
   Main PID: 983 (patroni)
      Tasks: 12 (limit: 1137)
     Memory: 101.8M
     CGroup: /system.slice/patroni.service
             ├─ 983 /usr/bin/python3 /usr/local/bin/patroni /etc/patroni/config.yml
             ├─3617 postgres -D /data/patroni --config-file=/data/patroni/postgresql.conf --listen_addresses=128.199.30.45 --port=5432 --cluster_name=postgres --wal_level=replica --hot_standby=on --max_connections=100 --max_wal_senders=&gt;
             ├─3619 postgres: postgres: startup   recovering 000000030000000000000005
             ├─3620 postgres: postgres: checkpointer
             ├─3621 postgres: postgres: background writer
             ├─3622 postgres: postgres: stats collector
             ├─3623 postgres: postgres: walreceiver   streaming 0/5000550
             └─3627 postgres: postgres: postgres postgres 128.199.30.45(57556) idle

Aug 29 04:23:11 do-02 patroni[983]: 2020-08-29 04:23:11,437 INFO: no action.  i am a secondary and i am following a leader
Aug 29 04:23:21 do-02 patroni[983]: 2020-08-29 04:23:21,432 INFO: Lock owner: node1; I am node2
Aug 29 04:23:21 do-02 patroni[983]: 2020-08-29 04:23:21,433 INFO: does not have lock
Aug 29 04:23:21 do-02 patroni[983]: 2020-08-29 04:23:21,437 INFO: no action.  i am a secondary and i am following a leader
Aug 29 04:23:31 do-02 patroni[983]: 2020-08-29 04:23:31,433 INFO: Lock owner: node1; I am node2
Aug 29 04:23:31 do-02 patroni[983]: 2020-08-29 04:23:31,433 INFO: does not have lock
Aug 29 04:23:31 do-02 patroni[983]: 2020-08-29 04:23:31,439 INFO: no action.  i am a secondary and i am following a leader
Aug 29 04:23:41 do-02 patroni[983]: 2020-08-29 04:23:41,434 INFO: Lock owner: node1; I am node2
Aug 29 04:23:41 do-02 patroni[983]: 2020-08-29 04:23:41,434 INFO: does not have lock
Aug 29 04:23:41 do-02 patroni[983]: 2020-08-29 04:23:41,439 INFO: no action.  i am a secondary and i am following a leader

</code></pre>
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
