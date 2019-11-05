#### LinuxProject

This is the documentation of the Project: Linux Server Configuration linked to the [Configuring Linux Web Servers](https://classroom.udacity.com/courses/ud299) course, which is part of Udacity's Fullstack Developer Nanodegree program.

My Linux Server is Ubuntu Xenial and configured as follows:

    IP address: 18.185.37.190
    SSH port: 2200
    URL to my web application: http://18.185.37.190.xip.io/

The installed software via system package manager: apt-get as follows:

    finger
    apache2
    libapache2-mod-wsgi
    postgresql
    python-pip
    git
    virtualenv

The installed software via Python package installer: pip as follows:

    sudo -H pip install -r /path/to/requirements.txt

#### The summary of configurations made:

*  Update all system packages.

*  Applying the security rule of Least Privilege, by disable root user remote login, stop logging in with password and use public key instead, finally adding some rules to the firewall.

*  Configure Apache to handle requests using the WSGI module, by editing the: /etc/apache2/sites-enabled/000-default.conf add the following lines at the end of: `<VirtualHost *:80>`:
    ```
    <VirtualHost *:80>
      #.
      #..
      #... other lines above

      # Add the following:
      # Turn on the wsgi pass authorization
      # Turn on handle requests using the wsgi module
      # Telling mod_wsgi to execute the flask application

      WSGIPassAuthorization On
      WSGIDaemonProcess itemcatalogapp user=www-data group=www-data threads=5
      WSGIScriptAlias / /var/www/FLASKAPPS/itemcatalogapp/itemcatalogapp.wsgi

      <Directory /var/www/FLASKAPPS/itemcatalogapp>
              WSGIProcessGroup itemcatalogapp
              WSGIApplicationGroup %{GLOBAL}
              Require all granted
      </Directory>
    </VirtualHost>
    ```
*  Configure Postgresql, by creating a new database: catalog as follows:

  Firstly: add a new system user: catalog , and full name: PostgreSQL user:
    ```
    sudo adduser catalog
    ```

  Secondly: creating the new database user: catalog & its associated database: catalog:
    ```
    sudo su postgres -c 'createuser -dRS catalog'
    sudo su catalog -c 'createdb'
    ```

  Thirdly: assign a password for the created database user: catalog:
    ```
    sudo su - postgres
    psql -U postgres catalog
    ALTER ROLE catalog PASSWORD 'ccatalog';
    ```

  Fourth: creating a database user for `www-data` its the default system user for Apache on Ubuntu:
    ```
    sudo su postgres -c 'createuser -dRS www-data'
    ```

  Fifth: making the database user `www-data` as a database supper user in order to be able to execute queries on the catalog database:
    ```
    sudo su - postgres
    psql -U postgres catalog
    ALTER ROLE "www-data" with SUPERUSER;
    ```

#### The list of third-party services:

*  [xip.io](http://xip.io/) service to get a DNS name that refers to my server's public IP address, for ex.: 18.185.37.190.xip.io resolves to 18.185.37.190

  That needed for set up OAuth for the web application.
