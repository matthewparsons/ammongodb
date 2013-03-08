ammongodb
=========

This is a plugin script which allows AMANDA to back up a Mongo Database.  To maintain the integrity of the backup, when performed on a running database, this should only be used on a read-only reporting node.

Sources
-------
* Mongo Backups: http://docs.mongodb.org/manual/administration/backups/
* AMANDA script API: http://wiki.zmanda.com/index.php/Script_API 
_x
Usage
-----
This script will be called by the AMANDA plugin API, snd should not be invoked directly.  The configuration file "amanda.conf" defines the input arguments in the "script-tool" and "dumptype" sections. 

For example, 

    define script-tool script-dump-mongo-replica {
        comment "Execute mongodump script"
        plugin "ammongodb"
        execute-where client
        execute-on pre-dle-backup, post-dle-backup
        property "port" "27018"
        property "auth" "/var/mongo/auth/mongo_backup_auth.cfg"
        property "log" "/var/logs/mongo/mongo-amanda.log"
    }

    define dumptype dump-mongo-replica {
        comp-user-tar
        comment "Mongo Dump"
        script "script-dump-mongo-replica"
        auth "bsdtcp"
    } 

Will execute this command, as seen in the amada sendbackup log file:

    /usr/libexec/amanda/application/ammongodb ammongodb POST-DLE-BACKUP \ 
        --execute-where client --config DailyBackup --host example.domain.net \
        --disk /var/mongo/dump --device /var/mongo/dump --level 0 --port 27018 \
        --auth /var/mongo/auth/mongo_backup_auth.cfg --log /var/log/mongo/mongo-amanda.log"

Options
-------
Most of the options are self-explanatory to users of Mongo.

* port: which the Mongo daemon listens on (standard for standalone is 27017; replica node, 27018)
* auth: file containing authentication credentials for the backup user, of the form:

    USER=<user>
    PASSWORD=<password>

The AMANDA "execute-on" key will take several values, but the ammongodb script will only recognise two:

* pre-dle-backup:  execute the <code>mongodump</code> command, dumping to the disk subdirectory mongo-backup-dump, backing up this directory to the AMANDA datastore.
* post-dle-backup: delete the mongo-backup-dump subdirectory
* log: file containing the backup script output; logs are also to be found in <code>/var/log/amanda/client/<config>/sendbackup*</code>.
