# Information model

See [Schemaspy documents](schemaspy)

## To generate schemaspy documents do something like this
```bash
java -jar schemaspy-6.1.0.jar -t pgsql -db paa_dev -host localhost -port 5532 -u paa -p mysoooosecretpassword -o ./schemaspy -dp postgresql-42.2.17.jar -s public -noads
```
