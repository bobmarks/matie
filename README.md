# M A T I E



Welcome to the `Readme.md` file for Matie! 



## 1. THE PROBLEM!

> _"Having to create another boring admin section to my app ..."_

Imagine a fairly typical software application consisting of a database and a customer facing UI.  You have spent months designing the perfect database schema, implementing innovative backend functionality, crafting a beautiful UI on top and ensuring a slick user experience!  However, the last piece of the puzzle is the boring (but important) admin section which provides a birds eye view of all of the application data stored in the various database tables ...  

In general, an admin client should provide the ability to browse the main database tables, provide various useful filters and support sorting of data. Server side pagination of bigger tables should also be supported.  Also, the ability to create, edit and delete rows in the various tables using user friendly forms which support validation.  Sometimes it makes sense to show sub views of data, especially if a table has lots of foreign key relationships.  Finally, users should be authenticated properly to the admin section and possibly get different views of the data depending on their role / level. 

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


### 3.4 Create [ _m ] database table

The following piece of SQL creates the `_m` database table which is required for the MATIE to run.
  
    create table _m (type  varchar (256),
                     name  varchar (256),  
                     value varchar (65535),
                     primary key (type, name));

All Matie configuration is inserted in the `_m` database table which can be bundled alongside your create / populate table scripts.  For example _(don't actually insert this into your database)_:- 

    insert into _m (type,    name,         value)        
            values ('table', 'cust_order', 'Customer Orders'),

The 3 columns names are as follows: - 

1. `type` - the type of configuration e.g. `table`  _(Definition of a database table in this example)_
2. `name` - the name of the configuration e.g. `cust_order` _(Name of database table in this example)_
3. `value` - the value of the configuration e.g. `Customer Orders` _(Human friendly label)_

Note, the combination of the `type` and `name` fields is the primary key.  


### 3.5. Run MATIE 

The default web interface can now be loaded using: -

* http://localhost:8080

At this stage you should say _"Authentication not configured"_. There are various ways to perform authenticate but the easiest is to insert the following into our `_m` database table: -

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
2. `tables` - a way to define multiple tables quickly. 
3. `view` - this isn't tied to a database table and but can display a view / query (but supports custom update queries).
4. `global` - this is used to set security, default values in `table` and `view` sections.


### 4.1 Matie `table` Definitions

Table definitions are used to add / remove database table definitions and also how the columns of each table and displayed / managed.

#### 4.1.1 Table Definitions Example - 2 Simple tables.

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

#### 4.1.3 Table Definition Example - A Complex example

Lets create a more complex screen. Pagination options are included and columns are included or hidden from views based on flags.    

    insert into _m (type,    name,   value)                     
            values ('table', 'room', '
    
    id : room-by-user
    label : Room

    columns: + TBL_* | - _PRIVATE* | + /[a-zA-Z0-9\d]*/

    column:
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

    filter: 
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

      update: |
        update room set 

    text: 
      head: Welcome to the room edit section of the client
      body: |
        There are various different ways that you can edit / create a new room. 
        For example, you can add a new room and delete another one.
      foot:
        Tip - deleting rooms may affect other sections of the site.

    actions: 
      reset-password: 
        
        input: text-input|Reset Password|Are you sure you want to reset the password? 
        # none | single | multi
        type: multi
        flags: L
        # Do we utilise a template language like free marker or here?  Could be super powerful.
        # Even more powerful would be a scripting langauge such as JavaScript
        sql: update users set password = ${input} where id in <#list ids as id>${id}</#id>
 
    params: 
      # this could be used for the link between a parent / child view
      # Not sure what to do here 

    flags: LSRN

    props: 
      can-edit : true
      can-delete : false
      debug-sql : true

    ');

> **NOTE**: It is essential to escape the SQL `insert` statements i.e. a single quotes are generally escaped 2 single quotes i.e. `name like ''%{value}%''`

Let discuss each section of this complex example in more detail: -

#### 4.2.4 Table Definition [ `id` ]

    id : room-by-user

The `id` of a table definition defaults to the name of the database table.  This optional field is thus only required if we required 2 (or more) editable screens created from the same database table.  The following will result in an error: -

    insert into _m (type,    name,   value) 
            values ('table', 'room', 'Rooms 1'),
                   ('table', 'room', 'Rooms 2);

However, the following will work because we specify an `id` for the second `room` entry: -

    insert into _m (type,    name,   value) 
            values ('table', 'room', 'Rooms 1'),
                   ('table', 'room', '
     
    id : room-2       

    ');

These `id` can be specified then in other parts of the definitions. 

#### 4.2.5 - Table Definition [ label ] 

    insert into _m (type,    name,             value) 
            values ('table', 'db_employ_data', '
 
    label    : Employee

    ');

The `label` is a human friendly version of the database table name (e.g. `Employee` is much nicer looking that `DB_EMPLOY_DATA`). 

If the label is the only thing specified in the `table` definition then we don't need to use a YAML configuration, this will also work: -


    insert into _m (type,    name,             value) 
            values ('table', 'db_employ_data', 'Employee');

> **NOTE (1)** - This is the only mandatory field, all the others are optional! 

> **NOTE (2)** - Also, if the `label` is the same as `table` then consider using the `tables` configuration type (see section 4.3). 

#### 4.2.6 - Table Definition [ columns ]

The `columns` attribute is a way to quickly include / exclude a number of columns in 3 different ways: -

1. **Include All** - include all columns using asterix `*` character.

    `columns: *`

    This is useful if we want to include all columns of a table but override the default functionality of only a few columns using the `column` attribute. If we don't declare this then _only_ the columns defined in each `column` will be displayed in the various admin screens.

2. **Simple list** - each column is seperated with pipe character e.g. 
 
        `columns: id | name | created | description` 
    
    This example includes the 4 columns specified.

3. **Matches** - columns can be matched via multiple stages of (a) simple wildcard matching (_uses asterisk `*` and question mark `?` characters_) and / or (b) advanced regular expression matching.  

    Each match must start with a plus `+` character (_include_) or a minus `-` character (_exclude_).  Each match is seperated with the pipe `|` character and the order is important (i.e. switching an include and an exclude around could create a different list of columns).

    This is best illustrated with an example: -

    `columns: +TBL_* | -*_PRIVATE??? | +/[a-zA-Z0-9_]*/`

    This example has 3 stages: -

    1. `+TBL_*`

        _include_ all columns which start with `TBL_` e.g. would include `TBL_EMPLOYEE` table. 
    
    2. `-*_PRIVATE???` 

        Take all the tables included from previous include and _excludes_ all the columns which end with `_PRIVATE` plus any 3 character e.g. would exclude the `EMP_PRIVATE029` table.

    3. `+/[a-zA-Z0-9_]*/`

        Takes all the tables matched from previous stage and includes columns using a regular expression (denoted by the start and end forward slashes `/ <regular_expression> /`.  This example uses a regex character class which matches tables containing all the lower and upper case letters of the alphabet, numbers 0 to 9 and the underscore `_` character e.g. would match `MANAGER_9` but not `$STAFF`.

    If you wanted _all_ columns except for e.g. 2 fields called `password` and `secret` then you would do the following: -

    `columns: -password | -secret`

    Due to the first match being an exclude then we assume that all columns are included initially (no initial asterix `*` required).

#### 4.2.7 - Table Definition [ column ] 

The column section is complex and enables the user to override default functionality for each column of a database table. 

    column:

      id:
        label : ID
        flags : LSRN
        input : text
        valid : empty
        p-key : true
        
      typeid:
        label : Type ID
        flags : ENF
        input : select | -1, - | 0..30..1

      min_sleeps:
        label: 
          SLF : Min. Sleeps
          EN  : Minimum Sleeps
        text  : The minimum number of people who can sleep 
        flags : ESLE
        input : select | ,- | select id,description from type
        valid : empty | Please select a valid minimum sleeps

      maxsleeps: Max Sleeps | SLFEN | select | ,- | select id,description from type 
     
      type_desc: Description | SLFEN | text area  

If we don't use the `columns` field (to match multiple columns) then only the fields defined in the `column` section are used.  If we use both `columns` to include e.g. 10 fields and `column` to override the behavour of e.g. 4 of these then the remaining 6 will use the default behavour (e.g. using textfields for input, column names for labels etc). 

If we have an `id` column as follows: -

    column:
      id:
        label : ID
        flags : LSRN
        ...

The fields that we can override are as follows: -

##### 4.2.7.1 - Table column definition [ label ] 

This is a user friendly label displayed in the various admin views e.g.

    label: Minimum Sleeps

This field is optional and defaults to the column name if not defined.  Also, we can have multiple labels defined in different views using flags i.e.

    label: 
      L : Min. Sleeps
      EN : Minimum Sleeps

In this example we have the shorter `Min. Sleeps` label defined in the _list_ (`L`) view and the longer `Minimum Sleeps` label is defined in the _edit_ (`E`) and _new_ (`N`) view.

> **NOTE** - `SNAKE_CASE` column names are converted to `Title Case` labels by default e.g. column `ROOM_TYPE_ID` would have a default label of `Room Type Id`.

##### 4.2.7.2 - Table column definition [ flags ] 

The `flags` is a useful way to specify which columns appear in which admin page views.  The main admin screens are: -

1. *List Page* - display a table of data.
2. *Edit Page* - form used to edit an existing item.
3. *New Page* - form used to create a new item.
4. *View Page* - read only version of a page.

For example, we may wish to show 4 columns in the list page, 8 in the new page and 6 in the edit page - this is achievable by specifying the correct flags in each column.

All the `column` flags are as follows: -

* `L` = Show is list view
* `S` = Column is sortable _(list view only - if specified then `L` is implied and therefore not required)_
* `E` = Show in edit view
* `R` = Show as read-only _(edit view only - if specified then `E` is implied and therefore not required)_
* `N` = Show in New view
* `V` = Show in display item view (optional readonly view)
* `I` = Inline editing _(list view only - if specified then `L` is implied and therefore not required)_

If no flags are displayed then the default is `LEN` (`list`, `edit` and `new` views).

> **NOTE** - The default column flags can be overriden in both the `table` and `global` (entire admin application).

##### 4.2.7.2 - Table column definition [ flags ] 

Most of the bulk of the configuration is in the `columns` section so a shorthand format exists to speed up declaration i.e.

    <database_column> : <Label> | <flags> | <input definition> | <validation> 

    maxsleeps : Max Sleeps | SLFEN | textarea | empty

If no flags are displayed then the default is every view (list, edit, new and view).

Most of the bulk of the configuration is in the `column` section so a shorthand format exists to speed up declaration i.e.

text|,-|select id, username from user order by username
textarea|,-|select id, username from user order by username
combobox|,-|select id, username from user order by username
combobox-multi|,-|select id, username from user order by username
chekcbox|,-|select id, username from user order by username
radio|,-|select id, username from user order by username



#### 4.2.<!!! FILL IN !!!> Table Definition [ `text` ]

The `text` section is used to create user friendly descriptions to your admin screens.  The main ones are `head` (top of screen), `body` (main description) and `foot` (bottom of screen).

    text: 
      head: Welcome to the room edit section of the client
      body: |
        There are various different ways that  
        you can edit / create a new room. 
        For example, you can add a new room and 
        delete another one.
      foot:
        Tip - deleting rooms may affect other sections of the site.

> **NOTE** - Multi-lines can be achieved by starting with a  pipe `|` character (e.g. see `body` section in example).

#### 4.2.<!!! FILL IN !!!> - Table Definition [ sql ] 

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

The `sql (list)` is used to override theis useful when a table of data contains lots of foreign keys e.g. `TYPE_ID` to other tables but we would prefer to display a more meaningful descriptive version of the key.  In this example we have our main table `room` where we link to the `room_type` table and display it's `t.description` description as `type_desc`. 

> **Note** - The column names still need to match up with the column names we wish to edit on even 




### 4.2 Matie `global` definitions

This is useful if you want to over

### 4.3 Matie `global` definitions

This is useful if you want to over

### 4.4 Matie `` definitions

Basdfasdf

### 4.5 Matie `global` definitions

This is useful if you want to override global definitions across your admin client.  These are sections of YAML which apply to the entire application but can be overriden in each section if desired.  For example: -

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