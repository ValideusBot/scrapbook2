# PostgreSQL

### general notes

    postgress -D /var/local/var/pg_db -! /tmp/pglog # -D specify  vher db is saved, -l where log is  saved
    psql
      \d  #list databases
      \d  my_db  #list tables of db 
      \d  table  #list colums
      \l    #list all databases
      

    mysql:  use database_name;
    postgres: \c database_name;
        
    mysql: SHOW TABLES
    postgresql: \d
    postgresql: SELECT table_name FROM information_schema.tables WHERE table_schema = 'public';

    mysql: SHOW DATABASES
    postgresql: \l
    postgresql: SELECT datname FROM pg_database;

    mysql: SHOW COLUMNS
    postgresql: \d table
    postgresql: SELECT column_name FROM information_schema.columns WHERE table_name ='table';

    mysql: DESCRIBE TABLE
    postgresql: \d+ table
    postgresql: SELECT column_name FROM information_schema.columns WHERE table_name ='table';


    rails new --database=postgres
    gem 'pg'
    1255




### create database & user & change root password
    
      CREATE USER tom WITH PASSWORD 'myPassword';
      CREATE DATABASE jerry;
      GRANT ALL PRIVILEGES ON DATABASE jerry to tom;
      
      ALTER USER postgres WITH PASSWORD 'happyface';
      
      \du  # list all users
      DROP USER username;  #you need to change owner of databases first
      
      # change owner of database
      ALTER DATABASE name OWNER TO new_owner;


higher changes with user in Postgress

      
      ALTER USER myuser WITH SUPERUSER; # will be superuser
      ALTER USER myuser WITH CREATEDB;  # can create db

http://www.postgresql.org/docs/current/static/sql-alterdatabase.html



### fedora start postgress

      psql --username=postgres

### Install postgresql 9.1 on Ubuntu 12.04
    
http://railskey.wordpress.com/2012/05/19/postgresql-installation-in-ubuntu-12-04/
    
    sudo apt-get install python-software-properties
    sudo add-apt-repository ppa:pitti/postgresql
    sudo apt-get update
    sudo apt-get install postgresql-9.1 libpq-dev
    
    #check instalation
    locate postgresql
    psql -V
    

### mint & ubuntu postgres 
    
    sudo -u postgres psql 
    
    # be sure to include host=localhost in config/database.yml with ubuntu

    # How to start / stop the server?
    sudo service postgresql start / stop
    sudo /etc/init.d/postgresql start / stop
    
### mac OSx postgres console 

    sudo -u tomi psql postgres    # tomi represent system user
 
source: https://zxmax.wordpress.com/2012/05/26/install-postgers-9-3-on-ubuntu-12-04/

 
### mint ubuntu start login to postgress   
     
     sudo -u postgres psql  # login from root
     
     psql -d postgres -U postgres --password --host=localhost


### add hstore to database


    sudo -u postgres psql my_app_development
    
    create extension hstore;

if you get `PG::Error: ERROR:  could not open extension control file "/usr/share/postgresql/9.1/extension/hstore.control": No such file or directory
: CREATE EXTENSION IF NOT EXISTS hstore` run:

    sudo apt-get install postgresql-contrib

source http://stackoverflow.com/questions/19467481/postgres-hstore-exists-and-doesnt-exist-at-same-time
