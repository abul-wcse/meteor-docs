[TOC]


**Meteor**
========

Meteor, a package builder and product deployment command line PHP tool, specifically designed for deploying products of Jadu and Spacecraft agency to the customer environment.
The product such as

 1. Jadu CMS
 2. Jadu XFP
 3. Spacecraft customer packages 

Meteor is single tool that take care of both building package and deployments. The part of meteor that performs  building packages are developed using **ANT** scripts and the other part that take care of deployments are developed using PHP script over the symphony framework as a console application.
 

**Adding Meteor to your project**
------------------------------------
Using composer is the recommended way to invoke Meteor to your project.

 - Add ***jadu/meteor*** as a dependency in your project's composer.json
   file. As of now, Meteor is not in the registered list of composer
   package. Hence include the repository in which Meteor resides, to
   your project's composer.json file,

```
{
 "repositories": [
    {
      "type": "git",
      "url": "https://Domain/meteor.git"
        }
    ],
    "require": {
	    "php": ">=5.3.2",
        "jadu/meteor": "1.0.5"
    }
}
```

 - Download and install Composer.

```
curl -sS https://getcomposer.org/installer | php
```

 - Install your dependencies.

```
php composer.phar install
```
**Initialising Meteor into your project**
--------------------------------------------
To be able to use Meteor you need add two 2 config files into your project
 
 1. build.xml that loads in the other scripts and sets some properties
 2. meteor.json file that provides settings for Doctrine Migrations

 To get template copies of these files run:
 ```
 ant -f vendor/jadu/meteor/res/build/ant/setup.xml setup-client
 ```
###**Building up the build.xml**
--------------------------------------

build.xml is the configuration file that is used while creating a package. 
There are two important parts on this configuration file. 

 1. Fileset , lists the files and directories that are to included or excluded in creation on package. 
 2. Targets, calls the tasks for necessary packaging. To find the list of task provided by meteor by default <a href="#ant-tasks">click here</a>

The default fileset 
```
<property name="version-file" value="${basedir}/CLIENT_VERSION" />
<include name="jadu/**" />
<include name="public_html/**" />
<include name="var/**" />
<include name="CLIENT_VERSION" /> 
<exclude name="public_html/site/styles/widget/**" />
``` 
To make  a custom fileset, uncomment the default fileset from the built.xml  update the fileset id to some *custom_fileset_id*. Update the list of files and directories that should be included and excluded in the package. Finally update the **package-fileset** property 

Example: 
```
<property name="version-file" value="${basedir}/CLIENT_VERSION" />
<fileset id="fileset.files" dir="${basedir}">
    <include name="jadu/**" />
    <include name="public_html/**" />
    <include name="var/**" />
    <include name="microsites/**" />
    <include name="CLIENT_VERSION" />
    <exclude name="public_html/site/styles/widget/**" />
</fileset>
<property name="package-fileset" value="fileset.files" />
```
>**Note:**
>It is alway recommended to include the version file of the package/module into the file set and also update the property version-file

### **Building up Meteor.json**
-------------------------------------
meteor.json is a configuration file that withholds the  details required for running a migration
```
{
   "migrations": {
        "name": "Migration Name",
        "table": "Migration Table",
        "namespace": "DoctrineMigrations",
        "directory": "upgrades/migrations"
   },
   "patch": {
        "includeHiddenFiles" : false
   }
}
```

>**NOTE.**

> - With the above example, Meteor will search the **upgrades/migrations** recursively for files that begin with Version followed one to 255 characters and a .php suffix. Version.{1,255}\.php is the regular expression that’s used. 
> - Everything after Version will be treated as the actual version in the database
> -  Take the file name Version<u><i>20150505120000</i></u>.php, **20150505120000** would be the version number stored in the migrations database table. 
> - Meteor uses Doctrine component for executing migrations, Doctrine creates a migration table inside the database and tracks which migrations have been executed there.
> - *includeHiddenFiles* is a boolean option, based on which Meteor decides wether the hidden file like .htaccess files. It is a replacement of the option   

###**Standards of naming migration tables**
-------------------------------------------------------
> - The standards of naming a migration table to work efficiently with Meteor while patch and rollback is that, the name of the table should be prefixed by <font color="red">**JaduMigrations** </font> suffixed by the product/module name <font color="red">**Core** </font> (or) <font color="red">**Xfp** </font> (or) <font color="red">**Epayments** </font>
> **Be aware of casing, the prefix JaduMigrations should alway look  same and the name of product/module should be in a casing structure that only its first letter can be capitalised**

**Baseline**
-------------------------
###**What is Baseline?**
The baseline is nothing else than a frozen picture of your project at a particular point time.
Baseline is the feature that store the state of each file on the package into a single file called *baseline.properties* as a particular point time. 
*baseline.properties* file consist of all file in the package and its relevant MD5.
If any file is altered the MD5 of the file changed and hence its easy to file the file that are altered after the creation of baseline.
This feature help to reduce the size of the package
###**How  to create a baseline?**
Once you have pulled the meteor dependency into your project use composer, run
```
ant package.create-baseline
```
or look for the relevant  task on build.xml, to see all task and its description. Meteor comes with a number of re-usable Ant scripts to help standardise the build process between projects. Tasks are run using ant `<taskname>`. To see the list of available tasks:
```
ant -p
```
Running the above command results in a file **baseline.properties** in *output* directory
Copy the file **baseline.properties** to your project's root directory and commit it.

##**Creating a Package**
----------------------------
The important task of any software development company is delivering their new product to the customer properly. The deliverable package is normally referred as binary package. In Meteor, packages are generated from the version control repository using a build language, <a href="http://ant.apache.org/">Apache ANT</a>. This package is deployed to client using the meteor console application. 

Creation of package is a ant tasks and it depends upon the `build.xml`. There are two types of packages that can be created

 1. Full package
 2. Baselined package

### **Full package**
Full package is a binary package that contains all script in the repository  that matches the file set defined on the `build.xml`. The first patch after a installation to a customer should be a `full-package`. 

####**How do I create a full package**
The base task used to create a full package is defined in the package.xml within Meteor.  The package requires two property,
1. Package name
2. Fileset

```
<target name="package" depends="package.load-version-number" description="Create a package ">

	<fileset id="fileset.files" dir="${basedir}">
	        <include name="jadu/**" />
	        <include name="public_html/**" />
	        <include name="var/**" />
	        <include name="CLIENT_VERSION" />
		    <exclude name="public_html/site/styles/widget/**" />
	</fileset>
	<property name="package-name" value="package.zip" />
	<property name="package-fileset" value="fileset.files" />
	<antcall target="package.package" />
</target>
```



###**Baselined package**
-------------------------------
Baselined package is a binary package that contains files/scripts that are altered after the baseline is created in the repository that matches the file set defined on the `build.xml`. The first patch after a installation to a customer should be a `package-baselined`. 

##**Meteor for deployment tasks**
------------------------------------------

###**Patching**
-----------------
The command to execute patching is
```
php meteor.phar patch:apply 
```
the parameters for the running this command,
Option | Description 
------------------------|----------------------|
 `--path` | Jadu installation path
`--patch-path `| path of the package
`--skip-lock` | Option to execute meteor ignoring lock file
`--include-hidden-files` | Patch hidden file like .htaccess 
`--skip-version-check` | Skip any version check that meteor does
`--skip-migrations`| Skip database migration
`--skip-file-migrations` | Skip file migration
`--post-slack`| Post the brief log on to the slack

While patching `--path` or / and `--patch-path` should be given based on the current directory.

- If you are in the {JADU_HOME}, then its required to give `--patch-path` is required.
- If your current directory is the patch directory, then `--path` option is required
- If your current directory is not either  patch directory nor {JADU_HOME} then both `--path` and `--patch-path` options are required

> **NOTE:**
> 
> - When you run the command during deployment, Doctrine will know exactly which migrations it hasn't run yet by looking at the migration table.
> - Check for the more details about dependancies <a href="">here<a>.

###**Remove Meteor lock**
-------------------------------
This command is used when you see the following error while performing patch or rollback, 
```
Creating lock file



  [RuntimeException]
  Unable to create lock file. This may be due to failure during a previous attempt to apply this package.



patch:apply [--path="..."] [--patch-path="..."] [--skip-lock] [--post-slack] [--skip-migrations] [--skip-file-migrations] [--db-name="..."] [--db-user="..."] [--db-password="..."] [--db-host="..."] [--db-driver="..."]
```

This is common error that occurs when someone else is performing patch or rollback action on the server or there was something wrong went in previous meteor execution. If you are sure that the state of server is perfect then you can remove the lock use the following command.

```
php meteor.phar patch:clear-lock --path={JADU_HOME}
``` 


###**Set permission to patch files**

If you want to set permission to the current patched files, then you can use this command. 

The command to invoke the above function in meteor is
```
php meteor.phar patch:set-permissions --path={JADU_HOME}
``` 

###Reset permission of files on the server

If you want to reset the permission of the files that resides in the server, you can use this command. 
>**Note:**
>It does not reset all permissions to all files in the Jadu Home, it tries to reset only group permissions to those list of files that are listed in config folder of Jadu.  

The command to invoke the above function in meteor is
```
php meteor.phar set-permissions --path={JADU_HOME}
``` 
###**File Migration Command**
-------
The a command to execute file migrations separately was introduced in Meteor 0.5. 
**file-migrations:migrate**

To run migrations from x version to y, then you can use 

`php meteor.phar file-migrations:migrate`

### **Commands related to migrations imported from Doctrine**
--------------------
  
##**<i class="icon-pencil">FAQs**
------------
<icon > **Release Notes of Meteor**
```
Meteor 0.1
 1. Skip Lock file
```
```
Meteor 0.2

 1. Skip database migration 
 2. Logging in Slack 
 3. Disk space validation 
 4. Reading system.xml to retrieve the database details
```
```
Meteor 0.3

 1. File Migrations as separate module 
 2. Skip File Migration 
 3. Maintaining all previous backups 
 4. Rollback to any of the preferred backup 
 5. Undo Rollback 
 6. Verifying the files that are copied to backup and live directory 
 7. Creation of an empty migration file without any config details
```

```
Meteor 0.4


```

```
Meteor 0.5

 1. Automatic detection of database driver.
 2.  New command for executing file migration alone
 3. New way to handle dependancies
 4. Skip Doctrine's migration conformation question if no migrations are to be executed
 5.  Including a option to patch or rollback without checking the previous state (version) of the sever
 6.  Patch dot file like .htaccess
 7. Added the addition exception message on request from support team
 8. Terminate the support of --quiet option
 9. Terminate meteor support if the database is DSN configured
 10. Setting Permissions on Window platform
 11. Show readable backup version on list on rollback
 12.  Enables XFP Rollback by fixing table not found exception
 13. Fixed issue where file system migrations get skipped in WISP.
```


----------


----------


**What are file migrations? Why are they needed?**

File migrations are a custom type of migration that Meteor supports, which are not a part of Doctrine migrations. File migrations are migrations that involve changes in files alone, not to the database. The main reason this feature is needed is to execute such migrations on all servers on which the Jadu CMS application is installed. If a client has multiple web nodes and the file system is not a Network File System (NFS) then the file changes need to be applied to each web node.

 If a Doctrine migration is used then the first node on which the patch or rollback is executed will get the appropriate changes and on the other web nodes the changes would have to be manually deployed. So the basic concept of file migration is to extract or separate the file changes from the database migrations.
	
**How does Meteor differentiate between file migrations and database migrations?**

Any migration class file within `upgrades/migrations/filesystem` is considered to be a file migration and the migration class files in `upgrades/migrations` are considered to be database migration files

**Is the usage of migrations:migrate command when MySQL goes away is same as migrations executed as a part of patch:apply command?**

No, they aren't same, migrations:migrate is only one of the steps. After the migrations:migrate command is executed, the current status of the migration should be determined and the status file that exists in the Jadu path should be updated. The current status of the migration can be determined using the migration:status command.

# **Common issues encounter while running Meteor**
----------------------------------------------------------

###**error: [apc-error] Cannot redeclare class**
If you get error: [apc-error] Cannot redeclare class… There is an issue with composer and apc Run the above script by overriding the php.ini value for apc.enable_cli in order to switch it off.

The command to do so:
```
php -d apc.enable_cli=0 meteor.phar patch:apply --path={JADU_HOME} --patch-path=/home/jadu_patch
``` 

###**Suhosin  enabled on server**
If the server is Suhosin  enabled. Run the following command:

```
php -d suhosin.executor.include.whitelist=phar meteor.phar patch:apply --path={JADU_HOME} --patch-path=/home/jadu_patch
```
###**phar extension disabled**
If phar extension is disabled and the version of PHP on the server is greater than 5.3. 

```
php -d extension=phar.so  meteor.phar patch:apply --path={JADU_HOME} --patch-path=/home/jadu_patch
```

###**MySQL Timeout**
If you get error: [PDOException] SQLSTATE[HY000]: General error: 2006 MySQL server has gone away

The issue is due to the connection timeout.

**Immediate step to be taken is to Rollback the failure patch**

There are 3 steps that needs to be carried for a proper and successful patch

**Step 1:** Run the patch without any migration using the option –skip-migration option

`php meteor.phar patch:apply --path={JADU_PATH} --skip-migration --skip-file-migrations`

**Step 2:** Run the both database and file migrations ,

To execute database migrations use
`php meteor.phar migrations:migrate --path={JADU_PATH}`

To run the file migrations [To run file migration you need meteor 0.5 or greater version ]
`php meteor.phar file-migrations:migrate --path={JADU_PATH}`

**Step 3:** Update the migrations version on the respective status file.

>**Note**
> The version just is executed at the end of each process is their respective current migration version. Update the respective file with the proper version.  If this step is missed the rollback might become a night mare.
>> **Example:** 
>>CORE_MIGARTION_NUMBER --> holds the current version of the database migration of Jadu CMS.

>>CORE_FILE_MIGARTION_NUMBER --> holds the current version of the file migration of Jadu CMS .

>>XFP_MIGARTION_NUMBER --> holds the current version of the database migration of XFP module.

>>XFP_FILE_MIGARTION_NUMBER --> holds the current version of the file migration of vXFP module.
>
>

**Another method**

If the system administrator are ok with increasing the  MySql time out , increase the value of wait_timeout from

`wait_timeout= 60` to `wait_timeout= 200`



Other Ant Tasks
-----------------

