# M A T I E



Welcome to the `Readme.md` file for Matie! 



## 1. THE PROBLEM!

> _"Having to create another boring admin section to my app ..."_

Imagine a fairly typical software application consisting of a database and a customer facing UI.  You have spent months designing the perfect database schema, implementing innovative backend functionality, crafting a beautiful UI on top and ensuring a slick user experience!  However, the last piece of the puzzle is the boring admin section which provides a birds eye view of all of the customer data stored in the various database tables ...  

In general, an admin client should provide the ability to browse the main database tables, provide various useful filters and support sorting of data. Server side pagination of bigger tables should also be supported.  Also, the ability to create, edit and delete rows in the various tables using forms.  Finally, users should be authenticated properly to the admin section and possibly get different views of the data depending on their role / level. 

Sound familiar?  Creating these admin screens can be tedious and may still take considerable time to implement and test, especially if the database schema is large.  

This common scenario is something which MATIE endeavors to solve.



## 2. THE SOLUTION!

Most admin screens consist of a seperate screen for each of the key database tables _(e.g. `CUSTOMER`)_ which supports the standard database CRUD _(Create, Retrieve, Update & Delete)_ functionality: -

1. **Create** view for adding a new database row which uses a nicely layed out form which includes validation and appropriate UI controls (e.g. a foreign key field e.g. `PRODUCT.ID` could be set via a combobox which displays a list of human readable `PRODUCT.DESCRIPTION` entries). You may offer a read-only view/s of single rows here too.
2. **Retrieve** a list view of the database table (including filters, pagination and sorting). 
3. **Update** view for amending an existing database row.  These screens are generally similar to the _Create_ view but may hide / show columns where appropriate.
4. **Delete** functionality.  The user can delete one or more rows from the list _(Retrieve)_ section.

MATIE helps define this functionality via a YAML based DSL (Domain Specific Language).  This DSL is stored in the database itself in a special MATIE database table (`_m`).  Using this DSL, it's possible to quickly create admin clients with zero programming code.


### 2.1. Advantages

1. **Create an admin UI for your app in minutes** - MATIE can be used to create a database driven user interface against any database schema in literally minutes - perfect for admin sections of sites / applications.
2. **Extremely easy to run / package** - Can run standaline as an executable Java JAR file or as a WAR file into e.g. Tomcat and connect up a database.  Can also be packaged as part of your software using a few lines of code. 
3. **2 Tier'd system**.  The backend layer interacts with the database via a REST-like interface. THE UI layer supports modern UIs e.g. HTML / JS using Bootstrap, Android, Apple, easily extensible server + client.
4. **Simple but powerful configuration** - created simply and easily as part of the database load scripts
5. **No coding required** - but completely extensible if necessary.
6. **YAML based DSL** - Can be generated in simple admin UI or hand coded for advanced users.
7. **Template driven or Generated UI** - the UI layer can be very simple (e.g. dynamically created at run-time) or  more complex / bespoke (i.e. UI source code generated from Matie which can then be modified).  


### 2.2. Disadvantages

Like all software MATIE is no "silver bullet" and may not be appropriate to: -

1. **Create highly bespoke UIs** or niche user interfaces - a programming language is more appropriate here.
2. **Non-database apps** - MATIE makes heavy use of a database so not useful for non-database applications.
3. **Another DSL to work with ...** - The MATIE DSL exists to provide a useful layer of abstraction but like any DSL there is always a cost to learning / maintaining the DSL itself. 



## 3. QUICK START!

Let's jump right in and try MATIE out!


### 3.1. Download a flavour of MATIE.

The easiest way to get started is to pick a "flavour" of MATIE.  We recommend the matie-vue-rt (run-time Vue based UI) and run: e.g.

    java -jar matie-vue-rt.jar 


### 3.2. Connect MATIE to a Database

This can be done in 3 ways.  

1. **`MATIE_HOME` environment variable + `matie.properties` property file**

    Create a `MATIE_HOME` environment variable which points to a folder where your `matie.properties` file lives.  e.g.

        MATIE_HOME=/opt/matie

    Inside the `/opt/matie` folder we can create a `matie.properties` file which contains something like the following (e.g. for PostgreSQL). 

        matie.jdbc.driver=com.mysql.jdbc.Driver
        matie.jdbc.url=jdbc:mysql://localhost:3306/my_database
        matie.jdbc.username=jack
        matie.jdbc.password=Jacks_Pwd

2. **Pure environment variables** 

    Alternatively we can create these 4 environment variables (e.g. `matie.jdbc.driver` or `MATIE_JDBC_DRIVER` also works) if you prefer not to create a configuration file. e.g.

3. **Java System Properties**

    Finally we can pass these in as system properties as follows: -
    
        java -jar matie-vue-rt.jar \
             -Dmatie.jdbc.driver=com.mysql.jdbc.Driver 
             -Dmatie.jdbc.url=jdbc:mysql://localhost:3306/my_database 
             -Dmatie.jdbc.username=jack 
             -Dmatie.jdbc.password=Jacks_Pwd

### 3.3 Example database (optional)

You may only want to work with your own database with Matie.  However, if you don't have one or you want to understand the following examples you can install the following simple example schema (link to another page).


### 3.4 Create [ _matie ] database table

The following piece of SQL creates the `_m` database table which is required for the MATIE to run.
  
    create table _m (type  varchar (256),
                     name  varchar (256),  
                     value varchar (65535),
                     primary key (type, name));

All Matie configuration is inserted in the `_m` database table which can be bundled alongside your create / populate table scripts.  For example _(don't actually insert this into your database)_:- 

    insert into _m (type,    name,         value)        
            values ('table', 'cust_order', 'Customer Orders'),

These 3 columns are follows: - 

1. `type` - the type of configuration e.g. `table`  _(Definition of a database table in this example)_
2. `name` - the name of the configuration e.g. `cust_order` _(Name of database table in this example)_
3. `value` - the value of the configuration e.g. `Customer Orders` _(Human friendly label)_

Note, the combination of the `type` and `name` fields is the primary key.  


### 3.5. Run MATIE 

The default web interface can now be loaded using: -

* http://localhost:8080

At this stage you should say _"Authentication not configured"_. There are various ways to performa authenticate but the easiest is to insert the following into our `_m` database table: -

    insert into _m (type,     name,       value) 
            values ('global', 'security', 'admin|admin123');

This configures a single admin username (`admin`) and password (`admin123`) in the database as plain text and is recommended only in development environments.

You should now see a fairly basic web based view of your database which includes every table and every column that your database contains.

The API is located at: -

* [localhost:8080/api](http://localhost:8080/api)

Also, the admin console can be accessed at: -  

* [localhost:8080/admin](http://localhost:8080/admin)



## 4. MATIE CONFIGURATION

You may be thinking to yourself - what's the point of this?  Is this simply a subset of a popular web based database admin tool such as phpmyadmin?  

And you would be right - running MATIE with no configuration is exactly that - a less powerful version of tools such as phpMyAmdin.  

However, it comes into it's own once you start playing with it's configuration.  This is achieved via `insert` containing YAML which can dynamically change the behaviour of the UI.  As mentioned about there is 3 columns (`type`, `name` and `value` - the list of available `type` values are as follows: -

1. `table` - this is the main type and describes a single database table.
2. `view` - this isn't tied to a database table and but can display a view / query (but supports custom update queries).
3. `global` - this is used to set security, default values in `table` and `view` sections.


### 4.1 Matie Table Definitions

Table definitions are used to add / remove database table definitions and also how the columns of each table and displayed / managed.

#### 4.1.1 Table Definitions Example - 2 Simple Views

    insert into _m (type,    name,        value) 
            values ('table', 'room',      'Rooms'),
                   ('table', 'room_type', 'Room Types');

This creates an admin UI with 2 views: - 

1. *Rooms* - mapped to the `room` table.
2. *Room Types* - mapped to the `room_type` table.

This is a table definition as it's most simple i.e. a database table mapped to a single label.  

#### 4.1.2 Table Definitions Example - More complex example

If there is more than 1 line in the `value` then we expect it to be in YAML format e.g.
 
    insert into _m (type,    name,   value)                    
            values ('table', 'room', '

    label   : Room
    columns : Id | Name

    ');
    
This example is also simple and illustrates we're only storing 2 columns (seperated by the pipe `|` character).

#### 4.1.3 Table Definition Example - Complex example

In the following example we have an advanced version of the list SQL, and custom behavour defined for each of the columns.  

    insert into _m (type,    name,   value)                     
            values ('table', 'room', '
    
    id : room-by-user
    label : Room

    text: -
      head: Welcome to the room edit section of the client
      body: |
        There are various different ways that you can edit / create a new room. 
        For example, you can add a new room and delete another one.
      foot:
        Tip - deleting rooms may affect other sections of the site.

    columns:
      id:
        label : ID
        flags : LSRN
        input : text
        valid : empty
        p-key : true
        sort  : desc
      typeid:
        label : Type ID
        flags : ENF
        input : select | -1, - | 0..30..1
      min_sleeps:
        label: 
          SLF : Min. Sleeps
          EN  : Minimum Sleeps
        text  : Show | 
        flags : ESLE
        input : select | ,- | select id,description from type
        valid : empty | Please select a valid minimum sleeps
      maxsleeps: Max Sleeps | SLFEN | select | ,- | select id,description from type      
      type_desc: Description | SLFEN | text area

    filters: 
      search:
        label : Search
        input : text
        sql   : (name like ''%{value}%'' or summary like ''${value}'' or contact_name like ''${value}'')
      topic:
        label : Topic
        input : select | ,All Types | select id, description from room_type order by description
        sql   : room_type_id = %${value}%

    page: 
      rows: 25
      counts: 10, 25, 100, 500
      show-count: true
      count-sql: count(1) from room r, type t where t.id = r.typeid

    sql:

      list: |
        select 
          r.id            id,
          r.typeid        typeid,
          r.min_sleeps    min_sleeps,
          r.max_sleeps    max_sleeps,
          t.description   type_desc
        from 
          room r
        inner join room_type on rt.id = t.id
        where user_id = ${user.id}  

      delete: |
        delete from room_info; 
        delete from room where id = ${id};

      update:
        update room set 

    actions: 
      reset-password: 
        
        input: text-input|Reset Password|Are you sure you want to reset the password? 
        # none | single | multi
        type: multi
        flags: L
        # Do we utilise a template language like free marker or here?  Could be super powerful.
        # Even more powerful would be a scripting langauge such as JavaScript
        sql: update users set password = ${input} where id in {{<#list ids as id>${id}</#id>}}
 
    params: 
      # this could be used for the link between a parent / child view

    flags: 

    props: 
      can-edit : true
      can-delete : false
      debug-sql : true

    ');

**NOTE**: it's necessary to escape single quotes with 2 single quotes e.g. 

    ...
    delete-sql : delete from room where id = ''{id}''
    ...
    
This creates a screen where the data can be filtered in 2 ways (a freeform text box and a database driven combo box). Pagination options are also included and columns are included or hidden from views based on flags.  Let's explain each section-by-section: -


#### 4.2.1 - Table Definition [ label ] 

    label    : Employee

The `label` is a human friendly version of the database table name (e.g. `Employee` is much nicer looking that `DB_EMPLOY_DATA`). 

> **NOTE** - This is the only mandatory field, all the others are optional! 


#### 4.2.2 - Table Definition [ sql ] 

    sql : |

      select 
        r.id            id,
        r.name          name,
        r.min_sleeps    min_sleeps,
        r.type_id       type_id,
        t.description   type_desc
      from 
        room r, type t
      where 
        t.id = r.typeid

The `list-sql` is useful when a table of data contains lots of foreign keys e.g. `TYPE_ID` to other tables but we would prefer to display a more meaningful descriptive version of the key.  In this example we have our main table `room` 

> **Note** - The column names still need to match up with the column names we wish to edit on even 


It's worth detailing each section of this 


    
NOTE the flags are as follows: -

    L = Show is list view
    S = Sortable (list view only)
    E = Show in edit view
    R = Show as read-only (edit view only)
    N = Show in new view
    V = Show in view item 
    I = Inline editing
    
If no flags are displayed then the default is everything (`list`, `edit`, `new` and `view`).

Most of the bulk of the configuration is in the `columns` section so a shorthand format exists to speed up declaration i.e.

    <database_column> : <Label> | <flags> | <input definition> | <validation> 

    maxsleeps : Max Sleeps | SLFEN | textarea | empty

If no flags are displayed then the default is every view (list, edit, new and view).

Most of the bulk of the configuration is in the `columns` section so a shorthand format exists to speed up declaration i.e.

####  - Global definitions

This is useful if you want to override global definitions across the codebase.  These are sections of YAML which apply to the entire application but can be overriden in each section if desired.  For example: -

    insert into _matie (type, name, value) values                    
                       ('global',  'table', '

    page: 
      rows: 25
      counts: 10, 25, 100, 500
      show-count: true

    flags: LSERNVI 

    props: 
      can-edit : true
      can-delete : false
      debug-sql : true

    ');

This example sets global `page`, `flags` and `props` for the application.  However, each table definition will override these if and when required.