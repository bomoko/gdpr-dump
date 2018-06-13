# GDPR Dump

Mysqldump lies at the heart of many of our daily data-centric tasks, as such, it’s an obvious place to do address data-sanitization (for, say, GDPR requirements).
This project aims to be a pure PHP drop-in replacement for mysqldump that tries to do that for you.

This project is forked from the proof-of-concept at [https://github.com/machbarmacher/gdpr-dump](https://github.com/machbarmacher/gdpr-dump),
but taken in a slightly different direction.
It is also built on top of [ifsnop/mysqldump\-php](https://github.com/ifsnop/mysqldump-php) which is a mysqldump compatible library.

## Usage 

Presently, this uses [Faker](https://packagist.org/packages/fzaninotto/faker) for the column sanitization.

Presently, the tool searches for the "gdpr-replacements" option, either passed as a command line argument, or as part of a [MySql options file](https://dev.mysql.com/doc/refman/8.0/en/option-files.html).

The "gdpr-replacements" option expects a JSON string with the following format

```
{"tableName" : {"columnName1": {"formatter": "Faker Formatter", ...}, {"columnName2": {"formatter": "Faker Formatter"}, ...}, ...}

```

### MySqlOptions file

If appearing in a MySql config file, you'll have it appear under the `[mysqldump]` section.

So, for example, you might have `/etc/my.cnf` with the following content

```
[mysqldump]
gdpr-replacements='{"fakertest":{"name": {"formatter":"name"}, "telephone": {"formatter":"phoneNumber"}}}'

```

### Command line argument

You're also able to pass the replacements as a command line argument.

```
>./mysqldump -uuser -p**** --gdpr-replacements='{"fakertest":{"name": {"formatter":"name"}, "telephone": {"formatter":"phoneNumber"}}}' --host=localhost testmysqldump;

... Will yeild ...

--
-- Dumping data for table `fakertest`
--

LOCK TABLES `fakertest` WRITE;
/*!40000 ALTER TABLE `fakertest` DISABLE KEYS */;
SET autocommit=0;
INSERT INTO `fakertest` VALUES (1,'Prof. Omari Kuphal','1-315-778-7545 x0547'),(2,'Ms. Aliza Powlowski Jr.','991.350.5517 x26999');
/*!40000 ALTER TABLE `fakertest` ENABLE KEYS */;
UNLOCK TABLES;
COMMIT;

```
