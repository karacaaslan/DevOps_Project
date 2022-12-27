Package.json Dosyası Oluşturma
Package.json dosyası projeniz ile bilgilerin tutulduğu json dosyasıdır. Bu json dosyasında projenizin adı, açıklaması, anahtar kelimeleri, git sayfası, lisansı, versiyonu, başlama dosyası ve test komutu bulunur. Bu dosyayı oluşturmak için;
$ npm init
komutunu girebilirsiniz. Oluşturulan package.json dosyası sizin proje klasöründe saklanır.

Yarn javascript projelerinde npm yerine kullanılabilecek daha kullanışlı, daha güvenli ve daha hızlı bir paket bağımlılık yöneticisi olarak tanımlanabilir.

Halihazırdaki npm projenizde yarn kullanmak için proje dizininizde yarn kodunu çalıştırmanız yeterli. Npm tarafından yarn’a geçmek birçok projede kolaydır. Çünkü yard da npm ile aynı package.json formatı ile çalışmaktadır. Proje dizininizde paketleri yüklemek için yarn veya yeni paket eklemek için yarn add <package> kodunu çalıştırdığınızda yarn yarn.lock dosyasını proje dizininde oluşturacaktır.

Özellikleri
Yarn daha önce indirilen paketleri yedekleyerek bir dahaki yüklemede daha hızlı yüklemenizi ve bu paketleri internete bağlı olmadığınızda dahi kurabilmenizi sağlar. Yarn ayrıca yüklenen paketlerin checksum değerlerini kontrol eder.

Kurulum
Homebrew kullanarak bilgisayarınıza yarn kurabilirsiniz.

brew install yarn
Eğer nvm gibi versiyonlama sistemi kullanıyorsanız --without-node parametresiyle kurulumu yapabilirsiniz.

brew install yarn --without-node
Kişisel seçimime gelicek olursak belli bir süre yarn kullandım daha sonra npm'e geçtim En son çıkan versiyonlarında yarn ile benzer özelliklerin geliştirilmiş olması ve gyp grpc gibi build kütüphanelerini kullanırken yaşadığım problemler neticesinde tamamen npm kullanmaya karar verdim.

Working Example (https://niole.net/how-to-load-csv-data-into-mysql-2bce9545822c)
We’re doing a working example first, because there is nothing worse than a long gabby treatise on “who-gives-a-shit” when you are “just trying to make it work”.

Goal
Load csv data into a new mysql server on startup. The implementation should be able to handle multiple tables, multiple rows per table, and special data types like enums and timestamps.

Dependencies
docker

The Command
------------------------------
docker run \
--name my-db \
-p 3306:3306 # for y'all trying to hit it from localhost
-v ./init.sql:/docker-entrypoint-initdb.d/init.sql \
-v ./init_data:/docker-entrypoint-initdb.d/init_data \
-e MYSQL_ROOT_PASSWORD=pw \
-e MYSQL_USER=my-database-user \
-e MYSQL_PASSWORD=pw \
-e MYSQL_DATABASE=my-database-name \
-d mysql:8.0.22 \
--secure-file-priv=docker-entrypoint-initdb.d
------------------------------

Important bits
— secure-file-priv=docker-entrypoint-initdb.d

secure_file_priv is a system variable. It tells mysql server that “it’s ok” to load data from files from the specified directory.

Why docker-entrypoint-initdb.d? When your mysql docker container starts for the first time, it will execute files in this directory with extensions .sql (as well as a couple others). Next we introduce the .sql file that we created.

init.sql

This file will contain your database and table initialization queries as well as your file data loading queries. You will see a syntax example later on.

init_data/

This directory contains your .csv data files. The init.sql code will reference these files in order to load their contents into mysql tables.

Code
Forgive the non-standard capitalization choices.

init.sql
CREATE DATABASE IF NOT EXISTS environment;
CREATE TABLE IF NOT EXISTS environment.Events (
Id varchar(36) PRIMARY KEY NOT NULL,
Type ENUM('THERMAL', 'PARTICLE') NOT NULL,
Unit ENUM('F','C','PM2.5') NOT NULL,
Timestamp timestamp NOT NULL
);
LOAD DATA INFILE '/docker-entrypoint-initdb.d/init_data/events.csv'
INTO TABLE environment.Events
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n' (Id,Type,Unit,Timestamp);