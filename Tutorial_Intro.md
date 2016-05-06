This document introduces how to download rouge-server and install it. It assumes you are confortable with some advanced computer tasks such as unziping a file, installing a service or a daemon and using MySQL.

# What is Rouge? #

Rouge Server is a highly customizable and modular Massively Multi-player Online (MMO) game server. An API is provided for Objective-C, Java and C#, making it ideal for iOs, Android and Windows Mobile. It provides basic communication services between players (instant messages, rooms, etc.), and various data storage/sharing schemes (persistent variables, mailboxes, etc.).

Rouge Server is licensed under the Apache Licenses 2.0 and uses a collection of libraries, most of them also under the Apache license or under the MIT license. There is also the MySQL JDBC connector, which is licensed under the GPL, under the FOSS exception.

# Getting and Installing Rouge #

In the download section, you will find the latest "stable" version of Rouge.
The download will be packaged as a zip file, which you can simply unzip
in the directory where you want to install Rouge.

Otherwise, you can checkout the latest copy of Rouge from the Subversion
repository. Also care is taken to keep the code in the repository stable, it's
highly likely to be buggy and broken. Compiling Rouge from source is easy and
only requires Java and Ant ( ant build ).

# Configuration #

Most of the configuration for Rouge can be found in the config.properties file
in the config file. From there, you can customize the server ports and available
server commands. Unless you have experience with Rouge, I wouldn't customize
the file too much. Just make sure you change the administration password.

Rouge also requires a MySQL database. The SQL script to initialize the database can be found in the script directory. Once you have the database setup, be sure to properly configure the config.properties with the appropriate database settings.

# Running #

On Linux, you can start the server using the start.sh schell script. On windows,
use the start batch file (to be added soon).

If you are working with the source from the SVN, you can also compile and
start the server using the "ant run" command.

## Running as a Daemon ##

In the scripts directory, you'll find a shell script that can be used to
configure a Java application as a Daemon. Until I finish configuring it,
you're on your own.

## Running as a Service ##

This section will be completed once I finish the appropriate wrappers. I'll probably use something like http://www.swtech.com/java/ntsvc/.

# Marking sure it works #

Once you have started the server, point a browser to http://hostname:8090/ where
hostname is the host name of the machine where Rouge is running. You should
see the administration console. The default username and password is "admin" and "admin".

# And beyond #

More information on how to use Rouge, and how to develop extensions can be
found in the Wiki section of the Rouge Server website at

http://code.google.com/p/rouge-server