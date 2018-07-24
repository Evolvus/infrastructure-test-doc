Datatabase backup and Archival

MongoDB Backup: 1.Create Directory
Ex:#mkdir -p /FLUX_CDA/cdadb/

2.Shell Scrtipt (backup.sh) to take the backup of cdadb database

Ex:

vim backup.sh #!/bin/sh

DIR=`date +%d%m%y` DEST=/FLUX_CDA/cda/$DIR mkdir $DEST

mongodump -h localhost -d cdadb -o $DEST zip -r $DEST.zip $DEST




Output:
1








2.





Note:Data will store in the form of json and bson format.

3.To Schedule the backup daily 8 pm in cronjob: root@db1:# crontab backup_cron

root@db1:# crontab -l

Output:
0 20 * * *	/FLUX_CDA/cdadb/backup.sh









4.Archiving:
1.Insert into the cdadbarch from cdadbcollection

Ex: >db.cdadbcollection.insert({'product_id':'2','ach_id':'NACH00000000006006','corporate_acc_no':'1 00101213','created_by':'system','created_date':' ','department_id':'AAA','mandate_category':'U001-Billpayment','product_code':'1020','product_name':'Auto-

loan','product_status':'Active','q_status':'ACC-300','reference_no':'','sponsor_bank_code':'UTIB0000248'});




5.To view the content of document in human readable format with below command:
Ex: >db.cdadbcollection.find().forEach(printjson)

Output:





































6.Insert into cdadbarch document from existing document.

> db.cdadbcollection.find({ product_id:"2" }). forEach( function(recs) { db.cdadbarch.insert(recs);});


Output:
> db.cdadbarch.find().forEach(printjson)




















Note:Please consider the screen shots as sample data.


APPENDIX

https://docs.mongodb.com/manual/administration/