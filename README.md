Kiji Enron Emails Dataset
=========================

## Background Information:

The Enron email dataset is a collection of ~500k emails, from ~150 Enron employees(mostly senior management).
It is probably one of the largest publically available datasets of "real" emails, which makes it interesting.

* Data collected from: https://www.cs.cmu.edu/~enron/
* Schema derived from http://www.isi.edu/~adibi/Enron/Enron_Dataset_Report.pdf
* Sentiment words derived from AFINN: http://www2.imm.dtu.dk/pubdb/views/publication_details.php?id=6010

## Loading the dataset into Kiji:

### Setup the environment:

    export ENRON_EMAIL_HOME=/path/to/kijienronemail
    export KIJI=kiji://.env/enron_email
    export LIBS_DIR=${ENRON_EMAIL_HOME}/target
    export KIJI_CLASSPATH="${LIBS_DIR}/*"
  
### Creating the tables in Kiji:

    kiji install --kiji=${KIJI}
    kiji-schema-shell --kiji=${KIJI} --file=${ENRON_EMAIL_HOME}/email_schema.ddl

### Run the importer:

    kiji jar ./target/enronemail-1.0-SNAPSHOT.jar org.kiji.enronemail.bulkimport.EmailBulkImporter kiji://.env/enron_email/emails maildir/

### Copy the sentiment file to HDFS

    hadoop fs -copyFromLocal AFINN-111.txt /tmp

### Run the sentiment producer:

    kiji produce --producer=org.kiji.enronemail.produce.SentimentProducer --input="format=kiji table=${KIJI}/emails" --output="format=kiji table=${KIJI}/emails nsplits=2" --lib=${LIBS_DIR}



