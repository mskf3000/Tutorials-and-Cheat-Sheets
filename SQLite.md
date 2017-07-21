## SQLite

By Jack Szwergold

Load the database:

    sqlite3 storage.db

Within the SQLite interface, export the database:

	.output storage_db.sql
	.dump
	.output stdout
	.exit

With the database exported, you should make a working copy of it instead of working directly on the `storage_db.sql` export; the source export should just be a clean backup. We’ll make a working copy of `storage_db.sql` and name it `storage_db_CLEAN.sql` like this:

    cp storage_db.sql storage_db_CLEAN.sql

Now open up `storage_db_CLEAN.sql` and edit it as you see fit like this:

    nano storage_db_CLEAN.sql

After your done editing the database, load it back into SQLite like this:

    sqlite3 storage.db < storage_db_CLEAN.sql

***

*SQLite (c) by Jack Szwergold; written on September 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*