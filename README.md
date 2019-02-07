s3-backup
==========

This repository contains two python applications, backup and restore.

backup
======
The ```backup``` command takes a dump of all MySQL databases and gzips them. 
It then creates a tarball of all of the SSL certificates, Keys and Certificate Signing Requests
within /var/www/\*/ssl/ and gzips that.

It then create an md5sum of these two files, and outputs them to the file 'checksum'.

The ```backup``` command then uploads these files to an S3 compatible backend.

expire
======
The ```expire``` command will connect to S3, and compare the filename of all of the folders for each host and identify ones that are older than 14 days old.
It will then prompt the user to delete the expired folders, and then will remove them from S3.

This should eventually be replaced with an automated utility, or when the CEPH API supports S3 Lifecycle 

restore
=======
The ```restore``` command scans S3 for all backups available for the current server (it uses the hostname of the server to determine which backups are available).

It then presents the user with numeric choices of all of the backups available:
```
Todays date is: Wednesday 04 March 2015

The following backups are available:
	[0] Tuesday 03 March 2015
	[1] Wednesday 04 March 2015

Which backup number do you wish to restore?
```

The user should choose which backup they wish to restore - then the ```restore``` command will create a folder in the current directory called 'restored' and download the backup to that directory.

Usage
=====
```
Usage: backup [OPTION...]
	 -h, --help 			 give this help list
	 -i, --init 			 force overwriting of S3 and MySQL Credentials
	 -n, --no-db 			 don't backup the database

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.
```

Credentials
===========
The first time you run the backup command, it will ask for S3 and MySQL credentials. 
It will then securely store these credentials for future use.

```backup --init``` - this will force the application to clear stored credentials and store new ones.
