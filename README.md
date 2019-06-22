# liquibase-csv-sequence

This project is an example to show that sequence usage in `<loadData />` with PostgreSQL is not working properly.
Liquibase will generate a proper SQL statement in migrate.sql but it can not execute the changeset correctly by itself.

## How to reproduce
1. start a PostgreSQL instance (I used docker to do so)

2. change `liquibase.properties` to match your DB settings

3. run `mvn clean compile liquibase:updateSQL` and check `target/migrate.sql` it contains the following line, which also is what I expected:
    ```
    INSERT INTO "user" (id, name) VALUES (nextval('id_seq'), 'user1'),(nextval('id_seq'), 'user2');
    ```

4. run `mvn clean compile liquibase:update` and you will get an error stating that you wanted to insert `NULL` as the value for `id`, which is not allowed by a constraint:
    ```
    [ERROR] Change Set db/changelog/db.changelog.xml::3::d3ns0n failed.  Error: java.sql.BatchUpdateException: Batch entry 0 INSERT INTO "user"(id, name) VALUES(NULL, 'user1') was aborted: ERROR: null value in column "id" violates not-null constraint Detail: Failing row contains (null, user1).  Call getNextException to see other errors in the batch.
    ```