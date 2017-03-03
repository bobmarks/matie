# M A T I E

Welcome to the `Readme.md` file for Matie! 

## 1. WHAT IS IT?

1. **Quickly generate UIs** - MATIE can be used to quickly create a database driven user interface - ideal for admin sections of sites / applications.
2. **Extremely easy to run** - drop WAR file into e.g. Tomcat and connect a database.
3. **2 Tier'd system** (server = rest) support modern UIs e.g. HTML / JS using Bootstrap, Android, Apple, easily extensible server + client.
4. **Simple but powerful configuration** - created simply and easily as part of the database load scripts
5. **Minimal coding** - but extensible if necessary.
6. **YAML based DSL**
7. **Template driven UI** - again configurable if necessary via templating e.g. Freemarker

## 2. WHAT IT'S NOT

1. **Use to create highly bespoke UIs** or niche user interfaces - a programming language is more appropriate here.
2. **Includes wizard support** - again, a programming language is more appropriate if these are required.

## 3. QUICK START!

The best way to try a new technology can help or not is usually to jump in and actually try it out.  The following are the steps requried to run *MATIE*: -

### 3.1. Install a Java Webserver e.g. Tomcat
The first step is to download and install your favourite Java web server e.g. I like Tomcat (http://tomcat.apache.org/)
 
### 3.2. Download MATIE WAR file
The easiest way to get started is to download the `matie.war` file into the correct folder of your web-server e.g. `/usr/lib/tomcat7/webapps`.

### 3.3. Connect MATIE to a Database

This can be done in 2 ways.  Create a `MATIE_HOME` environment variable which points to a folder where your `matie.properties` file lives.  e.g.

    MATIE_HOME=/opt/matie

Inside the `/opt/matie` folder we can create a `matie.properties` file which contains something like the following (e.g. for PostgreSQL). 

    matie.jdbc.driver=com.mysql.jdbc.Driver
    matie.jdbc.url=jdbc:mysql://localhost:3306/my_database
    matie.jdbc.username=jack
    matie.jdbc.password=Jacks_Pwd

Alternatively we can create these 4 e.g. `matie.jdbc.driver` environment variables if you prefer not to create a configuration file.

### 3.4 Example database (optional)

You may only want to work with your database and this is fine.  However, if you don't have one or you want to understand the proceeding examples 

### 3.5 Create a [ _matie ] database table in schema

The following piece of SQL creates the `_matie` database table which is required for the UI to run.
  
    create table _matie (type  varchar (256),
                         name  varchar (256),  
                         value varchar (65535),
                         primary key (type, name));

And that's it!  All configuration is inserted in the `_matie` database table which can be bundled alongside your create / populate table scripts.  It contains 3 fields: 

1. `type` - the type of configuration e.g. `table-definition`  _(Definition of a database table in this example)_
2. `name` - the name of the configuration e.g. `customer_order` _(Name of database table in this example)_
3. `value` - the value of the configuration e.g. `Customer Orders` _(Label in this example)_

Note, the combination of the `type` and `name` fields is the primary key.  

### 3.5. Run MATIE 

The default web interface can now be loaded using: -

* http://localhost:8080/matie/ui

You should now see a fairly basic web based view of your database which includes every table and every column that your database contains.

The API is located at: -

* http://localhost:8080/matie/api

Also, the admin console can be accessed at: -  

* http://localhost:8080/matie/admin

## 4. MATIE CONFIGURATION

You may be thinking to yourself - what's the point of this?  Is this simply a subset of a popular web based database admin tool such as phpmyadmin?  

And you would be right - running MATIE with no configuration is exactly that - a less powerful version of tools such as phpMyAmdin.  

However, it comes into it's own once you start playing with it's configuration.  

The following SQL `insert` examples will outline different configuration options which dynamically change the behaviour of the UI.  

### 4.1 Table Definitions

Table definitions are used to add / remove database table definitions and also how the columns of each table and displayed / managed.

#### 4.1.1 Table Definitions - 2 Simple Views

    insert into _matie (type, name, value) values 
                       ('table-definition', 'customer_order',  'Customer Orders'),
                       ('table-definition', 'user',            'Users');

This creates a UI with 2 views: - 

1. *Customer Orders* - mapped to `customer_order` table.
2. *Users* - mapped to `user` table.

This is a table definition as it's most simple i.e. a single label.  

#### 4.1.2 Table Definitions - More complex example

If there is more than 1 line in the `value` then we expect it to be in YAML format e.g.
 
    insert into _matie (type, name, value) values                    
                       ('table-definition',  'job', '

    label   : Job
    columns : Id | Name
    ');
    
This example isn't much more complex than the previous example.

#### 4.1.3 Table Definitions - Most complex example

In the following example we have an advanced version of the list SQL, and custom behavour defined for each of the columns.  

    insert into _matie (type, name, value) values                    
                       ('table-definition',  'room', '
    label    : Job
    list-sql : |
      select 
        r.id            id,
        r.name          name,
        r.beds          beds,
        r.typeid        typeid,
        r.sleeps        sleeps,
        r.min_sleeps    min_sleeps,
        t.description   type_desc,
        r.rate_type     rate_type,
        r.description   description
      from 
        room r, type t
      where 
        t.id = r.typeid
    delete-sql : delete from room where id = ''{id}''
    columns:
      id:
        label      : ID
        flags      : LSRN
        input      : text
        validation : empty
        pk         : true
      typeid:
        label      : Type ID
        flags      : ENF
        input      : select|-1,-|0..30..1
      minsleeps:
        label: 
          SLF : Min. Sleeps
          EN  : Minimum Sleeps
        flags      : ESLE
        input      : select|,-|select id,description from type
        validation : empty|Please select a valid minimum sleeps
    filters: 
      search:
        label : Search
        input : text
        sql   : (name like ''%{value}%'' or summary like ''%{value}%'' or contact_name like ''%{value}%'')
      topic:
        label : Topic
        input : select|,All Types|select id, description from room_type order by description
        sql   : room_type_id = %{value}%
    pagination: 
      rows-per-page: 25
      row-counts: 10, 25, 100, 500
      count-sql: count(1) from room r, type t where t.id = r.typeid
    actions: 
      can-edit : true
      can-delete : false
    debug: 
      sql: true
    ');

**NOTE**: it's necessary to escape single quotes with 2 single quotes e.g. 

    ...
    delete-sql : delete from room where id = ''{id}''
    ...
    
This creates a screen where the data can be filtered in 2 ways (a freeform text box and a database driven combo box).  

Pagination options are also included and columns are included or hidden from views based on flags.
    
NOTE the flags are as follows: -

    L    Show is list view
    S=Sortable (list view only)
    E=Show in edit view
    R=Show as read-only (edit view only)
    N=Show in new view
    
If no flags are displayed then the default is every view (list, edit and new).