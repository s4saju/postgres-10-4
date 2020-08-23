---


---

<h3 id="install-postgresql-10.4-using-source-code-in-ubuntu-linux-mint-or-centos-7">Install PostgreSQL 10.4 Using Source Code in Ubuntu, Linux Mint or Centos 7</h3>
<p>Major advantage of using source code installation is it can be highly customized during installation.</p>
<p><strong>1.</strong>  First install required prerequisites such as  <strong>gcc</strong>,  <strong>readline-devel</strong>  and  <strong>zlib-devel</strong>  using package manager as shown.</p>
<pre><code># yum install gcc zlib-devel readline-devel     [On RHEL/CentOS]
# apt install gcc zlib1g-dev libreadline6-dev   [On Debian/Ubuntu]
</code></pre>
<p><strong>2.</strong> Download the source code tar file from the official <a href="https://www.postgresql.org/download/">postgres website</a> using the following <a href="https://www.tecmint.com/10-wget-command-examples-in-linux/">wget command</a> directly on system.</p>
<pre><code># wget https://ftp.postgresql.org/pub/source/v10.4/postgresql-10.4.tar.bz2
</code></pre>
<p><strong>3.</strong> Use <a href="https://www.tecmint.com/18-tar-command-examples-in-linux/">tar command</a> to extract the downloaded tarball file. New directory named <strong>postgresql-10.4</strong> will be created.</p>
<pre><code> # tar -xf postgresql-10.4.tar.bz2
</code></pre>
<p><strong>4.</strong> Next step for installation procedure is to configure the downloaded source code by choosing the options according to your needs.</p>
<pre><code># cd postgresql-10.4
</code></pre>
<p><strong>5.</strong> Now install postgres using</p>
<pre><code> # ./configure.
</code></pre>
<h2 id="sample-output">Sample Output</h2>
<pre><code>checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking whether gcc supports -Wdeclaration-after-statement... yes
checking whether gcc supports -Wendif-labels... yes
checking whether gcc supports -Wmissing-format-attribute... yes
checking whether gcc supports -Wformat-security... yes
checking whether gcc supports -fno-strict-aliasing... yes
checking whether gcc supports -fwrapv... yes
checking whether gcc supports -fexcess-precision=standard... no
....
</code></pre>
<p><strong>6.</strong>  After configuring, next we will start to build postgreSQL using following  <strong>make command</strong>.</p>
<pre><code># make
</code></pre>
<h2 id="sample-output-1">Sample Output</h2>
<pre><code>make[2]: Entering directory '/root/postgresql-10.4/src/test/perl'
make[2]: Nothing to be done for 'all'.
make[2]: Leaving directory '/root/postgresql-10.4/src/test/perl'
make[1]: Leaving directory '/root/postgresql-10.4/src'
make -C config all
make[1]: Entering directory '/root/postgresql-10.4/config'
make[1]: Nothing to be done for 'all'.
make[1]: Leaving directory '/root/postgresql-10.4/config'
All of PostgreSQL successfully made. Ready to install.
</code></pre>
<p><strong>7.</strong> After build process finishes, now install postgresql using following command.</p>
<pre><code># make install
</code></pre>
<h2 id="sample-output-2">Sample Output</h2>
<pre><code>make[1]: Leaving directory '/root/postgresql-10.4/src'
make -C config install
make[1]: Entering directory '/root/postgresql-10.4/config'
/bin/mkdir -p '/usr/local/pgsql/lib/pgxs/config'
/usr/bin/install -c -m 755 ./install-sh '/usr/local/pgsql/lib/pgxs/config/install-sh'
/usr/bin/install -c -m 755 ./missing '/usr/local/pgsql/lib/pgxs/config/missing'
make[1]: Leaving directory '/root/postgresql-10.4/config'
PostgreSQL installation complete.
</code></pre>
<p><strong>Postgresql 10.4</strong>  has been successfully installed.</p>
<p><strong>8.</strong> Now create a postgres user and directory to be used as <strong>data</strong> directory for initializing database cluster. Owner of this data directory should be postgres user and permissions should be 700 and also set path for postgresql binaries for our ease.</p>
<pre><code># useradd postgres
# mkdir -p /usr/local/pgsql/data
# cd /usr/local/pgsql/
# chown -R postgres. data/
# cd /usr/local/pgsql/
# chown -R postgres:postgres data
# chown -R postgres:postgres bin
# echo 'export PATH=$PATH:/usr/local/pgsql/bin' &gt; /etc/profile.d/postgres.sh
</code></pre>
<p><strong>9.</strong> Now initialize database using the following command as <strong>postgres</strong> user before using any postgres commands.</p>
<pre><code># sudo su - postgres (for centos7 "su postgres")
# initdb -D /usr/local/pgsql/data -U postgres -W
</code></pre>
<p>Where  <code>-D</code>  is location for this database cluster or we can say it is data directory where we want to initialize database cluster,  <code>-U</code>  for database superuser name and  <code>-W</code>  for password prompt for db superuser.</p>
<p><strong>10.</strong>  After initializing database, start the database cluster or if you need to change port or listen address for server, edit the <code>postgresql-10.service</code> file</p>
<pre><code>vim  /etc/systemd/system/postgresql-10.service
</code></pre>
<p>Now copy the following &amp; save the file.</p>
<pre><code> [Unit]
        Description=PostgreSQL database server
        After=network.target

[Service]
Type=forking

User=postgres
Group=postgres

#Disable OOM kill on the postmaster
OOMScoreAdjust=-1000

Environment=PG_OOM_ADJUST_FILE=/proc/self/oom_score_adj
Environment=PG_OOM_ADJUST_VALUE=0

#Maximum number of seconds pg_ctl will wait for postgres to start.  Note that
#PGSTARTTIMEOUT should be less than TimeoutSec value.

Environment=PGSTARTTIMEOUT=270
Environment=PGDATA=/usr/local/pgsql/data
ExecStart=/usr/local/pgsql/bin/pg_ctl start -D ${PGDATA} -s -w -t ${PGSTARTTIMEOUT}
ExecStop=/usr/local/pgsql/bin/pg_ctl stop -D ${PGDATA} -s -m fast
ExecReload=/usr/local/pgsql/bin/pg_ctl reload -D ${PGDATA} -s

TimeoutSec=300

[Install]
WantedBy=multi-user.target
</code></pre>
<p><strong>11.</strong> Now, restart postgresql using following command</p>
<pre><code>systemctl restart postgresql-10.service
</code></pre>
<p><strong>12.</strong> Enable postgresql using following command.</p>
<pre><code>systemctl enable  postgresql-10.service
</code></pre>
<p><strong>13.</strong> Start the postgres service using following command.</p>
<pre><code>systemctl start  postgresql-10.service
</code></pre>
<p><strong>14.</strong> Check the postgres service status using following command.</p>
<pre><code>systemctl status postgresql-10.service
</code></pre>
<p><strong>15.</strong> Create a backup configuration file of pg_hba.conf</p>
<pre><code>root@system:~# cd /usr/local/pgsql/data/
root@system:/usr/local/pgsql/data# 
mv pg_hba.conf pg_hba.conf_backup
</code></pre>
<p><strong>16.</strong> Now change the the configuration file with following settings with <code>vim</code></p>
<pre><code>vim /usr/local/pgsql/data/pg_hba.conf
</code></pre>
<p>Copy, Paste &amp; save the following commands to <code>pg_hba.conf</code></p>
<pre><code>#PostgreSQL Client Authentication Configuration File
    # ===================================================
    #
    # Refer to the "Client Authentication" section in the PostgreSQL
    # documentation for a complete description of this file.  A short
    # synopsis follows.
    #
    # This file controls: which hosts are allowed to connect, how clients
    # are authenticated, which PostgreSQL user names they can use, which
    # databases they can access.  Records take one of these forms:
    #
    # local  DATABASE  USER  METHOD  [OPTIONS]
    # host DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
    # hostssl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
    # hostnossl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
    #
    # (The uppercase items must be replaced by actual values.)
    #
    # The first field is the connection type: "local" is a Unix-domain
    # socket, "host" is either a plain or SSL-encrypted TCP/IP socket,
    # "hostssl" is an SSL-encrypted TCP/IP socket, and "hostnossl" is a
    # plain TCP/IP socket.
    #
    # DATABASE can be "all", "sameuser", "samerole", "replication", a
    # database name, or a comma-separated list thereof. The "all"
    # keyword does not match "replication". Access to replication
    # must be enabled in a separate record (see example below).
    # USER can be "all", a user name, a group name prefixed with "+", or a
    # comma-separated list thereof.  In both the DATABASE and USER fields
    # you can also write a file name prefixed with "@" to include names
    # from a separate file.
    #
    # ADDRESS specifies the set of hosts the record matches.  It can be a
    # host name, or it is made up of an IP address and a CIDR mask that is
    # an integer (between 0 and 32 (IPv4) or 128 (IPv6) inclusive) that
    # specifies the number of significant bits in the mask.  A host name
    # that starts with a dot (.) matches a suffix of the actual host name.
    # Alternatively, you can write an IP address and netmask in separate
    # columns to specify the set of hosts.  Instead of a CIDR-address, you
    # can write "samehost" to match any of the server's own IP addresses,
    # or "samenet" to match any address in any subnet that the server is
    # directly connected to.
    #
    # METHOD can be "trust", "reject", "md5", "password", "gss", "sspi",
    # "krb5", "ident", "peer", "pam", "ldap", "radius" or "cert".  Note that
    # "password" sends passwords in clear text; "md5" is preferred since
    # it sends encrypted passwords.
    #
    # OPTIONS are a set of options for the authentication in the format
    # NAME=VALUE.  The available options depend on the different
    # authentication methods -- refer to the "Client Authentication"
    # section in the documentation for a list of which options are
    # available for which authentication methods.
    #
    # Database and user names containing spaces, commas, quotes and other
    # special characters must be quoted.  Quoting one of the keywords
    
    # "all", "sameuser", "samerole" or "replication" makes the name lose
    # its special character, and just match a database or username with
    # that name.
    #
    # This file is read on server startup and when the postmaster receives
    # a SIGHUP signal.  If you edit the file on a running system, you have
    # to SIGHUP the postmaster for the changes to take effect.  You can
    # use "pg_ctl reload" to do that.   
    # Put your actual configuration here
    # ----------------------------------
    #
    # If you want to allow non-local connections, you need to add more
    # "host" records.  In that case you will also need to make PostgreSQL
    # listen on a non-local interface via the listen_addresses
    # configuration parameter, or via the -i or -h command line switches.
    #cal" is for Unix domain socket connections only
        host all all  0.0.0.0/0  md5
        # IPv4 local connections:
        host  all all 127.0.0.1/32  md5
        local all all md5
        host all  postgres  127.0.0.1/32 md5
        #host  all all 127.0.0.1/32  ident
        # IPv6 local connections:
        host  all all ::1/128 md5
        #host  all all ::1/128 ident
        # Allow replication connections from localhost, by a user with the    
    # replication privileged
    local replication all peer
    host  replication all 127.0.0.1/32  ident
    host  replication all ::1/128 ident   
</code></pre>
<p>Now, give the file permission of pg_hba.conf using following command.</p>
<pre><code>cd /usr/local/pgsql/data/
chown -R postgres:postgres pg_hba.conf
</code></pre>
<p>Now, Restart the <code>postgresql-10.service</code> using <code>systemctl</code></p>
<pre><code>systemctl restart postgresql-10.service
</code></pre>
<p><strong>17.</strong> Now connect to database cluster and create database by using following commands.</p>
<pre><code>root@system:~# su - postgres
$ psql
Password: 
psql (10.4)
Type "help" for help.
postgres#
postgres=# CREATE DATABASE prismcas;
CREATE DATABASE
postgres=# 
</code></pre>
<p>----End----</p>

