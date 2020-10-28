<!--TODO&#10;&#10;&#10;Hi Vikram,&#10;&#10;I think this will be a great tutorial! Thank you for the work you have put in to date. I'm including some notes for changes to the structure and some of the descriptions through to step 6.&#10;&#10;Could you also work on making it 100% clear which node the reader should be working on at any given point? I'm including some guidelines below. I will continue my technical test once you have completed this review and the changes are in place.&#10;&#10;Look forward to seeing the next draft.&#10;&#10;&#10;&#10;For referring to the nodes:&#10;&#10;1. Names such as node-1, node-2, let's add them in bold **node-1**.&#10;2. Tell the reader before the command which node to run the command on.&#10;3. And to make triple sure we're making the node usage clear to the reader let's use the different environment colors on the commands.&#10;&#10;Perhaps for commands run on multiple nodes, leave as standard command box,&#10;&#10;[environment second]&#10;&#10;[environment third]&#10;&#10;etc.&#10;&#10;You can check these in the markdown previewer: https://www.digitalocean.com/community/markdown&#10;&#10;otherwise, it looks as though we'll only be accessing node-4 and node-5 separately? Step 6, it isn't clear to me which node we should run the commands on.&#10;&#10;&#10;&#10;&#10;&#10;&#10;-->
<h1 id="how-to-set-up-a-highly-available-postgresql-cluster-on-ubuntu-using-patroni-and-haproxy">How To Set Up a Highly Available PostgreSQL Cluster on Ubuntu Using Patroni and HAProxy</h1>
<h3 id="introduction">Introduction</h3>
<!--Introductory paragraph about the topic that explains what this topic is about and why the reader should care; what problem does it solve?-->
<!--Introductory paragraph about the topic that explains what this topic is about and why the reader should care; what problem does it solve?-->
<p>PostgreSQL is an opensource relational database that can run on major operating systems. It is highly robust and versatile, but doesn’t have features for the <a href="https://www.digitalocean.com/community/tutorials/what-is-high-availability">high availability</a>.</p>
<p>Patroni can provide high availability for PostgreSQl. Patroni is a template for you to create your own customized, high-availability solution using Python.</p>
<p>With the high availability solution that has more than one database instances in a single cluster, it will be difficult to maintain the database end points. To solve this, <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts">HAProxy</a> can be used. It keeps track of changes in the Master/Slave nodes and connects to the appropriate master node when the clients request connection.</p>
<p>In this tutorial, you’ll set up the PostgreSQL with high availability using Patroni and HAProxy.</p>
<p>When you’re finished, you will have a robust and <a href="https://www.digitalocean.com/community/tutorials/what-is-high-availability">highly available</a> PostgreSQL cluster ready for production use.</p>
<h2 id="prerequisites">Prerequisites</h2>
<p>Before you begin this guide, you’ll need the following:</p>
<ul>
<li>
<p>Five Ubuntu 20.04 serverset up by following <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">the Ubuntu 20.04 initial server setup guide</a>, including a non-root <code>sudo</code>-enabled user and a firewall. Initially diskspace available in the droplet would be sufficient. If required, additional volumes can be added.</p>
</li>
<li>
<p>Install PostgreSQL on three of your servers by following <a href="https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart">the Install PostgreSQL on Ubuntu 20.04</a>. Just follow Step 1 of PostgreSQL installation tutorial. Other steps should be ignored. These three servers are referred as <strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong> in this tutorial.</p>
</li>
</ul>
<!-- Vikram Q: I hope you have followed all the steps in this tutorial rather following only the first step. That's why you are facing the invalid user issue in the later point of this tutorial. &#10;&#10;Since we have only one command from the tutorials, shall we add the step directly in our tutorial? -->
<ul>
<li>
<p>Ensure Python 3 is available on your three servers where PostgreSQL is installed by following the guide <a href="https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server">Install Python 3 and Set Up a Programming Environment on an Ubuntu 20.04 Server</a>.</p>
</li>
<li>
<p>Reserve two servers for installing etcd and HAProxy. We will refer to these servers as <strong>node-4</strong> and <strong>node-5</strong> respectively.</p>
</li>
</ul>
<h2 id="step-1-—-configuring-your-node-firewalls">Step 1 — Configuring Your Node Firewalls</h2>
<p>You need to configure <a href="https://www.digitalocean.com/docs/networking/firewalls/#features">firewalls</a> in your droplets to restrict the Incoming and the Outgoing traffic to the droplets.</p>
<p>DigitalOcean Cloud Firewalls are a network-based, stateful firewall service for Droplets. You can learn How to create a firewall rules, edit the firewall rules in <a href="https://www.digitalocean.com/docs/networking/firewalls/how-to/configure-rules/">this DigitalOcean cloud firewall tutorial</a>.</p>
<p>Now, let’s see how to configure the firewall in the nodes.</p>
<h3 id="configuring-ports-for-postgresql">Configuring ports for Postgresql</h3>
<p>Create a rule to allow inbound traffic from the nodes(<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>,<strong>node-5</strong> ) via port <code>8008</code>.</p>
<ul>
<li>Patroni’s REST API uses port <code>8008</code> to provide the health checks and cluster messaging between the participating nodes.</li>
</ul>
<p>Create a rule to allow inbound traffic from the nodes(<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>, <strong>node-5</strong> ) via port <code>5432</code>.</p>
<ul>
<li>Postgresql listens to port <code>5432</code> for the database transactions and and Data Replication.</li>
</ul>
<p>Apply the firewall to the PostgreSQL nodes(<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>).</p>
<ul>
<li>This allows PostgreSQL nodes to communicate between each other for creating replicas.</li>
</ul>
<p>Apply the firewall to the HAProxy node(<strong>node-5</strong>).</p>
<ul>
<li>This allows HAProxy node to communicate with the active Master postgresql node in the cluster.</li>
</ul>
<p>The below image shows the complete firewall set up for the postgresql.</p>
<p><img src="https://imgur.com/UFBfpvA" alt="Postgresql firewall"></p>
<h3 id="configuring-ports-for-etcd">Configuring ports for ETCD</h3>
<p>Create a rule to allow inbound traffic from  the nodes (<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>) via port <code>2379</code>.</p>
<ul>
<li>ETCD uses the port <code>2379</code> for the client requests. Clients communicate with ETCD using this port to read and write information from ETCD. For e.g. Database connection details, current leader node information, node status etc.</li>
</ul>
<p>Apply the firewall to the ETCD node(<strong>node-4</strong>).</p>
<ul>
<li>This allows ETCD node to communicate with the Postgres client node in the cluster.</li>
</ul>
<p>The below image shows the complete firewall set up for the ETCD.</p>
<p><img src="https://imgur.com/5yWGPTC" alt="ETCD firewall"></p>
<h3 id="configuring-ports-for-haproxy">Configuring ports for HAProxy</h3>
<p>Create a rule to allow inbound traffic from All IPV4 and IPV6 address via port <code>7000</code>.</p>
<ul>
<li>HAproxy uses the port 7000 to serve the client requests for the database transactions. If you already know your client IP Address, then you can use only that IP address in the source. This will ensure only the traffic from your client is served by HAProxy and other requests are dropped.</li>
</ul>
<p>Apply the firewall to the HAProxy node(<strong>node-5</strong>).</p>
<ul>
<li>This allows HAproxy node to serve the requests for the database transaction.</li>
</ul>
<p>The below image shows the complete firewall set up for HAProxy.</p>
<p><img src="https://imgur.com/tAwZe70" alt="Postgresql firewall"></p>
<p>The firewall configuration is complete for the Highly available postresql cluster. You now more on and start setting up the cluster.</p>
<h2 id="step-2-—-stopping-postgres-service">Step 2 <strong>—</strong> Stopping Postgres Service</h2>
<p>When PostgreSQL in installed, it automatically starts as a system service. You should stop this PostgreSQL service so that Patroni can take care of running the PostgreSQL Service.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services. Use <code>stop</code> with <code>systemctl</code> to stop the system service.</p>
<p>Execute the following command to stop the PostgreSQL service.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl stop postgresql
</code></pre>
<p><strong>Note:</strong> You should execute this command in all three droplets(<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>)where PostgreSQL is installed.</p>
<p>Now the PostgreSQL service is stopped in all nodes(<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>).</p>
<p>You can install Patroni, so that it takes charge of running PostgreSQL Service as required.</p>
<h2 id="step-3-—-installing-patroni">Step 3 <strong>—</strong> Installing Patroni</h2>
<!-- For more information on steps, see https://do.co/style/#steps -->
<p>Patroni is a Python package which can be used to manage the PostgreSQL configuration. Patroni is capable of handling Database replication, backup and restoration configurations.</p>
<p>Patroni uses utilities that come installed with postgres, located in the  <strong>/usr/lib/postgresql/&lt;<sup>&gt;12&lt;</sup>&gt;/bin</strong>  directory by default. You need to create symbolic links in the PATH to ensure that Patroni can find the utilities.</p>
<p><code>ln</code> command with <code>-s</code> option is used to create symbolic links. Execute the below command to create the symbolic link.</p>
<pre class=" language-command"><code class="prism  language-command">sudo ln -s /usr/lib/postgresql/&lt;^&gt;12&lt;^&gt;/bin/* /usr/sbin/
</code></pre>
<p>Make sure you replace postgresql version according to the version installed.</p>
<p>The  <code>pip3 install</code>  command is used to install additional Python packages.</p>
<p>Execute the following command to install <strong>Patroni</strong> along with its dependencies <strong>psycopg2-binary</strong> and <strong>python-etcd</strong>.</p>
<pre class=" language-command"><code class="prism  language-command">sudo pip3 install patroni psycopg2-binary python-etcd
</code></pre>
<p><strong>Note:</strong>  You should execute this command in all three droplets(<strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>) where PostgreSQL is installed, so that the PostgreSQL configuration can be handled using Patroni.</p>
<p>Now, you can install the etcd to handle the distributed cluster.</p>
<h2 id="step-4-—-installing-etcd">Step 4 <strong>—</strong> Installing etcd</h2>
<p>etcd is a strongly consistent, distributed key-value store that provides a reliable way to store data that needs to be accessed by a distributed system or cluster of machines. It gracefully handles leader elections during network partitions and can tolerate machine failure, even in the leader node.</p>
<p>The <code>apt-get install</code> command is used to install packages with all the necessary dependencies.</p>
<p>Execute the following command to install etcd in the <strong>node-4</strong> reserved for etcd.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fourth]
sudo apt install etcd
</code></pre>
<p>etcd is installed successfully. Now, you can install HAProxy that provides high availability.</p>
<h2 id="step-5-—-installing-haproxy">Step 5 <strong>—</strong> Installing HAProxy</h2>
<p>HAProxy is free, open source software that provides a high availability load balancer and proxy server for TCP and HTTP-based applications that spreads requests across multiple servers.</p>
<p>The <code>apt install</code> command is used to install packages with all the necessary dependencies.</p>
<p>Execute the following command to install HAProxy in the <strong>node-5</strong> reserved for HAProxy:</p>
<pre class=" language-command"><code class="prism  language-command">[environment fifth]
sudo apt install haproxy
</code></pre>
<p>HAProxy is installed successfully.</p>
<p>Now all the installations are complete in the relevant servers and you’ll start the configuration of etcd, Patroni and HAProxy.</p>
<h2 id="step-6-—-configuring-etcd">Step 6 <strong>—</strong> Configuring etcd</h2>
<p>etcd is a fault-tolerant, distributed key-value store that is used to store the state of the postgres cluster. It gracefully handles leader elections in the cluster and can tolerate machine failure, even in the leader node.</p>
<p>You’ve installed etcd in the step3.</p>
<p>Now, you will configure etcd to handle the leader elections in the highly available cluster and store the state of the postgres cluster.</p>
<p>Using Patroni, all of the postgres nodes makes use of etcd to keep the postgres cluster up and running.</p>
<p>During the installation of the etcd, a default etcd configuration file is created in the location <em>/etc/default/etcd</em>.</p>
<p><code>nano</code> tool is used edit the file. Use  <code>sudo nano</code> to open the file in the edit mode. If you do not use <code>sudo</code>, nano will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fourth]
sudo nano /etc/default/etcd
</code></pre>
<p>nano opens the file.</p>
<p>Now the etcd default configuration file is opened where all the parameters are commented. Look for the each of the below parameters, uncomment it and update the settings with the relevant etcd droplet IP address as give below.</p>
<pre><code>[label /etc/default/etcd]
ETCD_LISTEN_PEER_URLS="http://&lt;^&gt;node-4-server-ip&lt;^&gt;:2380"
ETCD_LISTEN_CLIENT_URLS="http://localhost:2379,http://&lt;^&gt;node-4-server-ip&lt;^&gt;:2379"
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://&lt;^&gt;node-4-server-ip&lt;^&gt;:2380"
ETCD_INITIAL_CLUSTER="default=http://&lt;^&gt;node-4-server-ip&lt;^&gt;:2380,"
ETCD_ADVERTISE_CLIENT_URLS="http://&lt;^&gt;node-4-server-ip&lt;^&gt;:2379"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
</code></pre>
<p>Save the changes to the file and exit the nano editor.</p>
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
<p>Execute the following command to restart the etcd service.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fourth]
sudo systemctl restart etcd
</code></pre>
<p>Now etcd is running with the updated configurations.</p>
<p>Use <code>enable</code> with <code>systemctl</code> to enable automatic start of etcd service after every system reboot.</p>
<p>Execute the following command to enable automatic start of etcd service after system reboot.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fourth]
sudo systemctl enable etcd
</code></pre>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the system service.</p>
<p>Execute the following command to check the status of the etcd service.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fourth]
sudo systemctl status etcd
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
<h2 id="step-7-—-configuring-patroni">Step 7 <strong>—</strong> Configuring Patroni</h2>
<p>Patroni is a Python package used to handle PostgreSQL configuration in the high availability clusters. You’ve already installed Patroni in the Step 3 in <strong>node-1</strong>, <strong>node-2</strong>, <strong>node-3</strong>.</p>
<p>Now, you will configure Patroni  <em>using a YAML file</em>  in the  <em>/etc/patroni/</em> directory  to handle the PostgreSQL service in <strong>node-1</strong>. A default YAML file is available in the offical Patroni GitHub  <a href="https://github.com/zalando/patroni/blob/master/postgres0.yml">URL</a>.</p>
<p>Create a directory patroni inside the /etc/ folder using the <code>mkdir</code> command as below.</p>
<pre class=" language-command"><code class="prism  language-command">sudo mkdir /etc/patroni
</code></pre>
<p>You should navigate to the /etc/patroni/ directory to copy the YAML file to that location.  <code>cd</code>  command can be used to navigate to the specified directory.</p>
<p>Execute the following command to navigate to the  <em>/etc/patroni/</em>  directory.</p>
<pre class=" language-command"><code class="prism  language-command">cd /etc/patroni/
</code></pre>
<p>Now, your current working directory is /etc/patroni/.</p>
<p>Next, you need to copy the raw  <a href="https://raw.githubusercontent.com/zalando/patroni/master/postgres0.yml">default YAML</a>  file from GitHub to the /etc/patroni/ directory.</p>
<p><code>curl</code>  tool is used to copy data from a server to another server.</p>
<p>Execute the below command to copy the YAML file from GitHub to your server.</p>
<pre class=" language-command"><code class="prism  language-command">sudo curl -o config.yml https://raw.githubusercontent.com/zalando/patroni/master/postgres0.yml
</code></pre>
<p>The -o option in the  <code>curl</code>  command copies the file with the filename specified in the command. Here it creates a file named config.yml.</p>
<p>Now, you need to update the config.yml  file with the right configuration.</p>
<p><code>nano</code>  tool is used edit the file. Use  <code>sudo nano</code>  to open the file in the edit mode. If you do not use  <code>sudo</code>, nano will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre class=" language-command"><code class="prism  language-command">sudo nano config.yml
</code></pre>
<p>nano opens the file.</p>
<p>Update the highlighted parameters in the config.yml.</p>
<pre><code>[label /etc/patroni/config.yml]
scope: &lt;^&gt;postgres&lt;^&gt;
&lt;^&gt;namespace: /db/&lt;^&gt;
name: &lt;^&gt;node1&lt;^&gt;

restapi:
  listen: &lt;^&gt;node-1-server-ip&lt;^&gt;:8008
  connect_address: &lt;^&gt;node-1-server-ip&lt;^&gt;:8008
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
  host: &lt;^&gt;node-4-server-ip&lt;^&gt;:2379
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
  &lt;^&gt;- host replication replicator node-1-server-ip/0 md5&lt;^&gt;
  &lt;^&gt;- host replication replicator node-2-server-ip/0 md5&lt;^&gt;
  &lt;^&gt;- host replication replicator node-3-server-ip/0 md5&lt;^&gt;
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
  listen: &lt;^&gt;node-1-server-ip&lt;^&gt;:5432
  connect_address: &lt;^&gt;node-1-server-ip&lt;^&gt;:5432
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
<p>You can learn more about Patroni configuration parameters in the <a href="https://patroni.readthedocs.io/en/latest/SETTINGS.html">official patroni docs page</a>. Here’s what each line in this file is for in the different sections of the file.</p>
<p>Global section:</p>
<ul>
<li>scope - Name for the highly available postgres cluster</li>
<li>namespace -path within the configuration store where Patroni will keep information about the cluster. Default value: “/service”.</li>
<li>name - the name of the host. Must be unique for the cluster. for e.g. (<strong>node-1</strong> in the first server, <strong>node-2</strong> in the second server and so on).</li>
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
<li>authentication - Authentication section has the username and passwords specified for the superuser, replication user.<br>
SuperUser is a user which has no access restrictions in the Postgresql database. Superuser is used by Patroni to connect to the postgresql database.<br>
Replication user is a user which has permissions to perform the replication between the master and slave nodes and used by replicas to access master via streaming replication.<br>
You can specify the passwords for the superuser and replication user in the password properties of the users respectively.</li>
</ul>
<p>Now, save the changes to the file and exit the nano editor.</p>
<p>Next, you need to configure the data directory.</p>
<p>In the Patroni configuration file, data directory is specified as <em>/data/patroni</em>. The superuser(<em>postgres</em>) mentioned in the configuration file, should be able to write into the data directory.</p>
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
<p>You need to create a file <em>/etc/systemd/system/patroni.service</em> using the nano command.</p>
<pre class=" language-command"><code class="prism  language-command">sudo nano /etc/systemd/system/patroni.service
</code></pre>
<p>nano opens the file.</p>
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
WantedBy=multi-user.target
</code></pre>
<p>As highlighted above, you need to update the location of the Patroni configuration file.  You can learn more about the systemd unit and unit file in <a href="https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files">this</a> tutorial.</p>
<p>Save the changes to the file and exit the nano editor.</p>
<p>Now, you need to start Patroni for handling the postgres database.</p>
<p>The <code>systemctl</code> command is used to manage the <em>systemd</em> services.</p>
<p>Use <code>start</code> with <code>systemctl</code> to start the system service.</p>
<p>Execute the following command to start Patroni.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl start patroni
</code></pre>
<p>Now Patroni is running and its handling the postgresql database.</p>
<p>Use <code>enable</code> with <code>systemctl</code> to enable automatic start of Patroni service after every system reboot.</p>
<p>Execute the following command to enable automatic start of Patroni service after system reboot.</p>
<pre class=" language-command"><code class="prism  language-command">sudo systemctl enable patroni
</code></pre>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the system service.</p>
<p>Execute the following command to check the status of the Patroni service.</p>
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
<p>Configuring Patroni in the first Droplet is complete.</p>
<p>You need to follow the same steps in other two droplets(<strong>node-2</strong> and <strong>node-3</strong>) where PostgreSQL is installed.</p>
<p>The specific sections to be updated are</p>
<ul>
<li>Node name</li>
<li>IP Address of the respective node in the <strong>restapi</strong> section and the <strong>PostgreSQL</strong> section.</li>
</ul>
<p>Once you complete the Patroni configuration in the <strong>node-2</strong> and <strong>node-3</strong>, these nodes will join in the cluster and follow the leader <strong>node-1</strong>.</p>
<p>You can use <code>status</code> with the <code>systemctl</code> to check the status of the Patroni in <strong>node-2</strong>.</p>
<p>Execute the following command to check the status of the Patroni service.</p>
<pre class=" language-command"><code class="prism  language-command">[environment second]
sudo systemctl status patroni
</code></pre>
<p>You will see the below messages if the <strong>node-2</strong> is joined in the cluster successfully.</p>
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
<p>Postgresql cluster is up and running.</p>
<p>Now, you’ll configure HAProxy which can be used to connect to Master Postgresql node.</p>
<h2 id="step-8-—-configuring-haproxy">Step 8 <strong>—</strong> Configuring HAProxy</h2>
<p>HAProxy is free, open source software that provides a high availability load balancer and proxy server for TCP and HTTP-based applications that spreads requests across multiple servers…</p>
<p>You can use HAProxy to connect to the master node in the configured Postgresql cluster. All Postgres clients (your applications, <code>psql</code>, etc.) will connect to HAProxy which will make sure you connect to the master in the configured postgres cluster. You’ve installed it in <strong>node-5</strong> in the step4 of the turorial.</p>
<p>During the installation of the HAProxy, a default haproxy.cfg file is created in the location <em>/etc/haproxy/haproxy.cfg</em>.</p>
<p><code>nano</code> tool is used edit the file. Use  <code>sudo nano</code> to open the file in the edit mode. If you do not use <code>sudo</code>, nano will open the file in the read only mode.</p>
<p>Execute the below command to open and update the configuration file.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fifth]
sudo nano /etc/haproxy/haproxy.cfg
</code></pre>
<p>nano opens the file.</p>
<p>Update the file with the following content.</p>
<pre><code>[label /etc/haproxy/haproxy.cfg]
global
    maxconn 100

defaults
    log global
    mode tcp
    retries 2
    timeout client 30m
    timeout connect 4s
    timeout server 30m
    timeout check 5s

listen stats
    mode http
    bind *:7000
    stats enable
    stats uri /

listen postgres
    bind *:5000
    option httpchk
    http-check expect status 200
    default-server inter 3s fall 3 rise 2 on-marked-down shutdown-sessions
    server postgresql_&lt;^&gt;node-1-server-ip&lt;^&gt;_5432 &lt;^&gt;node-1-server-ip&lt;^&gt;:5432 maxconn 100 check port 8008
    server postgresql_&lt;^&gt;node-2-server-ip&lt;^&gt;_5432 &lt;^&gt;node-2-server-ip&lt;^&gt;:5432 maxconn 100 check port 8008
    server postgresql_&lt;^&gt;node-3-server-ip&lt;^&gt;_5432 &lt;^&gt;node-3-server-ip&lt;^&gt;:5432 maxconn 100 check port 8008
</code></pre>
<p>In the listen postgres section, you’ll update the details of the Postgres servers IPs which is used by HAProxy to connect to Postgres master server. You can learn more about HAProxy configuration parameters in the <a href="http://cbonte.github.io/haproxy-dconv/2.2/configuration.html#2.5">official HAProxy docs page</a>.</p>
<p>Save the changes to the file and exit the nano editor.</p>
<p>Now, you need to restart HAProxy for handling the high availability with the updated settings.</p>
<p>The  <code>systemctl</code>  command is used to manage the  <em>systemd</em>  services. Use  <code>restart</code>  with  <code>systemctl</code>  to start the system service.</p>
<p>Execute the following command to restart the HAProxy.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fifth]
sudo systemctl restart haproxy
</code></pre>
<p>Use <code>enable</code> with <code>systemctl</code> to enable automatic start of HAProxy service after every system reboot.</p>
<p>Execute the following command to enable automatic start of HAProxy service after system reboot.</p>
<pre class=" language-command"><code class="prism  language-command">[environment fifth]
sudo systemctl enable haproxy
</code></pre>
<p>Now  <em>HAProxy</em>  is running and its handling the postgresql database instance with High availability.</p>
<p>Now, you can test the highly available postgresql cluster.</p>
<h2 id="step-9-—-testing-the-setup">Step 9 <strong>—</strong> Testing the Setup</h2>
<p>You can test the highly available cluster using the HAProxy dashboard.</p>
<p>In your preferred web browser, enter the http://&lt;<sup>&gt;node-5-IP-address&lt;</sup>&gt;:7000 to open the HAProxy dashboard which looks like the below image.</p>
<p><img src="https://imgur.com/o0KFtlO" alt="HAproxy dashboard page"></p>
<p>In the postgres section, the green highlighted line denotes the postgres node which is currently acting as the master. Now, shutdown the <strong>node-1</strong> or stop the Patroni service in the <strong>node-1</strong>, you’ll be able to see another node from the cluster becomes the master. Refresh the HAProxy dashboard page and you’ll see the <strong>node-2</strong> which has become the master as shown below.</p>
<p><img src="https://imgur.com/4ZxTY8X" alt="HAproxy dashboard page2"></p>
<p>When you restart the <strong>node-1</strong> or start the Patroni service, then <strong>node-1</strong> will join the cluster, sync and follow the leader <strong>node-2</strong>.</p>
<p>You now have the highly available postgres up and running.</p>
<p>Your applications and postgres client can use the &lt;<sup>&gt;public-IP-address&lt;</sup>&gt; of the <strong>node-5</strong> where HAProxy is installed with port 5000 to connect to this highly available cluster.</p>
<h2 id="conclusion">Conclusion</h2>
<p>In this article, you have set up a robust and highly available PostgreSQL cluster.</p>
<p>Now you can improve it further by</p>
<ul>
<li>adding larger etcd cluster to improve availability</li>
<li>adding HAProxy server and configure IP failover to create a highly available HAProxy cluster</li>
</ul>

