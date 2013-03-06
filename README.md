ammongodb
=========

AMANDA plugin script to back up a Mongo Database

This script will be called by the AMANDA plugin API, specified in the amanda.conf file for the backup set.

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

