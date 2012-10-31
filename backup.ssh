#!/bin/bash
############

#AMAZON ID
AMAZONID=ENTERYOURAMAZONIDHERE

#S3 BUCKET 
BUCKET=bucketname

#SHORT WEBSITE URL (no funny characters)
SITE=domain.com

#SERVER USERNAME
USER=username

#SQL USERNAME AND PASSWORD
userpassword=" --user=mysql_username --password=mysqlpassword"

#SQL DATABASES (to use more than 1 put spaces between databases)
databases="one_database another_database"
dumpoptions=" --quick --add-drop-table --add-locks --extended-insert --lock-tables"

################
#Don't Modify Below Here
################

#directories
DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
backupdir=/home/$USER/backup


mysqldumpcmd=/usr/bin/mysqldump
DOW=`date +%Y%m%d`

#dump SQL Files
for database in $databases
do
$mysqldumpcmd $userpassword $dumpoptions ${database} > ${backupdir}/sql/${DOW}-${database}.sql
done

# Compress all of our backup files
for database in $databases
do
rm -f ${backupdir}/${DOW}-${database}.sql.gz
rm -f ${backupdir}/sql/${DOW}-${database}.sql.gz
$gzip ${backupdir}/${DOW}-${database}.sql
done

#Make www Files
tar -czvf /home/$USER/backup/${DOW}-$SITE.tar.gz /home/$USER/public_html ${backupdir}/sql

#Put file on s3
$DIR/s3/s3-put -k $AMAZONID -s "$DIR/s3.key" -T /home/$USER/backup/${DOW}-$SITE.tar.gz -v /$BUCKET/$SITE-${DOW}.tar.gz

#removing temp files
rm ${backupdir}/sql/*
rm /home/$USER/backup/${DOW}-$SITE.tar.gz
rm -rf /tmp/s3-*