---
layout: post
published: true
title: Importing data into PyroCMS using Migrations
date: 2011-12-08 00:50:00
categories:
- web-development
tags:
- codeigniter
- pyrocms

---

A client asked me to import a bunch of data from his old CMS into PyroCMS. His old system had insecure passwords and a bunch of data that was tied to the user via the user id. I wrote a Migration to pull the data out of his old tables, register a secure PyroCMS user, and record the user&#39;s uploads and other data. Here&#39;s how and why...

---


CodeIgniter's Migrations make this a fairly simple task. `$this` is available in the migration so you can easily load models, libraries, dbforge and so on to help process or insert the data.

To do this create a new migration named `070_Import.php` and drop it into `system/cms/migrations`. (PyroCMS' migrations are at 69 at the present). Adjust it to one number higher than the highest in the migration folder.

The migration file's contents should look like this:

    <?php defined('BASEPATH') OR exit('No direct script access allowed');

    class Migration_Import extends CI_Migration {

        public function up()
        {
            // import logic here
        }

        public function down()
        {
            // undo the import here
        }
    }

Now import the old tables into the main database and move all of the files (images, zips, or whatever) assocciated with the records into a folder within `uploads/`. Now you can write the logic to pull the data out of the old table, clean it up if need be, and then insert it into a PyroCMS' table. Uploads can be renamed and copied to a different folder in `uploads/`.

When you are ready to try the import simply open `system/cms/config/migration.php` and increment the `migration_version` to one number higher. Reload the page and the `up()` method in the migration will run. 

More than likely your import won't go properly the first time you try due to oversites or mistakes in your code. This is where the down() method comes in. Write a couple of lines of code that delete the newly created records and the newly copied files in `uploads/`. If your import fails, lower the number in the config file by 1 and reload the page. Make necessary tweaks to your import code, increment the number in the config, and reload the page.

When you are confident that the import is finished you can delete the migration file. You then need to open your database and look for a table named `migration_version` or `schema_version` depending on the version of PyroCMS. Change its value and the migration config file's value back to what it was before you started this import exercise.
