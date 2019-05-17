# CoBRA migrations

Over the past few weeks we have learned how to use `rake db:create`, `rake db:migrate` and other `rake` database tasks to modify our project databases. However, since Nitro is made up of components, many of which have their own unique databases, the tools Nitro Developers use to run migrations are slightly different.

You can still think of Nitro as one central data center that stores Power's information. But the technical reality is that Nitro is a collection of many smaller databases that are all stitched together.

Bookmark [this powerhome StackOverflow Q/A right now](https://stackoverflow.com/c/powerhome/questions/114) and use it as reference when making database changes inside `nitro-web`.

## Adding migrations to Nitro

New migration files can live in two different places:

1. If your migration is a structural change, meaning that you are adding a column or creating a new table, your migration file should live in the `db` directory in the component the associated Rails `Model` lives in. Nitro Developers may call this type of migration a "structural migration".
1. If your migration simply adds data, such as creating a new row or rows in an existing table, then your migration file should live in the `db` directory on root. Nitro Developers call the root directory of `nitro-web` "Umbrella" because it holds all the components. Nitro Developer may call this type of migration a "data migration".

When running a migration file in Umbrella, all you need to do is run `bin/rake db:migrate` on root. When running a migration file to a component, then you first need to run `bin/rake db:migrate` in the component's root directory, and then in Umbrella. To generate a migration file in Nitro, you can still use `rails g migration <MigrationFileName>`, whether you are adding it to a component or to Umbrella. Just use the command in the correct folder.

Nitro's data gets updated daily with new records and structure. So when it comes time for you to add a new migration, you should almost always start with reseting your local database. When reseting your local db, you start by pulling down a local data dump using the `bin/rake dev:load_recent` task from Umbrella. After this finishes, you reset the local database(s) for the component(s) you are working on using `bin/rake app:db:component:reset`. This must be run from the component's root directory.

## Checking the new database structure

Instead of updating a `schema.rb` file, Nitro uses a `structure-self.sql` file convention. As the file extension implies, `structure-self.sql` are files that contain SQL that are more specific about the data they describe. These files should never be manually altered. Like the `schema.rb`, changes to these files are automated by executing migration files.
