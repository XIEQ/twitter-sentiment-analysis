Twitter Sentiment Analysis
==========================

* retrieve tweets using Spark Streaming
* language detection
* sentiment analysis (StanfordNLP)   
* index tweets in Elasticsearch 
* live dashboard using Kibana

UPDATE Alonso

First at all, thank you to Vincent Spiewak, original author of this project. 

I am running this code in osx Sierra, so the best way is to install kibana and elastic dependencies using brew command.

    brew install kibana

    brew install elasticsearch

Go to spark.apache.org and download spark-1.5.2-bin-hadoop2.6 version.

Go to https://apps.twitter.com and create an app, save in your laptop consumer-key, consumer-secret, access_token and access_token_secret values.

To run the code:

Execute elasticsearch:

    $:~ aironman$ elasticsearch 
    [2016-10-03 16:49:49,879][INFO ][node                     ] [Rex Mundi] version[2.4.0], pid[52071], build[ce9f0c7/2016-08-29T09:14:17Z]
    [2016-10-03 16:49:49,880][INFO ][node                     ] [Rex Mundi] initializing ...
    [2016-10-03 16:49:50,425][INFO ][plugins                  ] [Rex Mundi] modules [reindex, lang-expression, lang-groovy], plugins [], sites []
    [2016-10-03 16:49:50,454][INFO ][env                      ] [Rex Mundi] using [1] data paths, mounts [[/ (/dev/disk1)]], net usable_space [33.6gb], net total_space [464.7gb], spins? [unknown], types [hfs]
    [2016-10-03 16:49:50,454][INFO ][env                      ] [Rex Mundi] heap size [989.8mb], compressed ordinary object pointers [true]
    [2016-10-03 16:49:50,455][WARN ][env                      ] [Rex Mundi] max file descriptors [10240] for elasticsearch process likely too low, consider increasing to at least [65536]
    [2016-10-03 16:49:52,143][INFO ][node                     ] [Rex Mundi] initialized
    [2016-10-03 16:49:52,143][INFO ][node                     ] [Rex Mundi] starting ...
    [2016-10-03 16:49:52,220][INFO ][transport                ] [Rex Mundi] publish_address {127.0.0.1:9300}, bound_addresses {[fe80::1]:9300}, {[::1]:9300}, {127.0.0.1:9300}
    [2016-10-03 16:49:52,224][INFO ][discovery                ] [Rex Mundi] elasticsearch_aironman/vzEnJrBbRbqkQGdGts10tQ
    [2016-10-03 16:49:55,252][INFO ][cluster.service          ] [Rex Mundi] new_master {Rex Mundi}{vzEnJrBbRbqkQGdGts10tQ}{127.0.0.1}{127.0.0.1:9300}, reason: zen-disco-join(elected_as_master, [0] joins received)
    [2016-10-03 16:49:55,264][INFO ][http                     ] [Rex Mundi] publish_address {127.0.0.1:9200}, bound_addresses {[fe80::1]:9200}, {[::1]:9200}, {127.0.0.1:9200}
    [2016-10-03 16:49:55,264][INFO ][node                     ] [Rex Mundi] started
    [2016-10-03 16:49:55,316][INFO ][gateway                  ] [Rex Mundi] recovered [3] indices into cluster_state
    [2016-10-03 16:49:55,827][INFO ][cluster.routing.allocation] [Rex Mundi] Cluster health status changed from [RED] to [YELLOW] (reason: [shards started [[library][2]] ...]).
    [2016-10-03 16:50:25,279][WARN ][cluster.routing.allocation.decider] [Rex Mundi] high disk watermark [90%] exceeded on [vzEnJrBbRbqkQGdGts10tQ][Rex Mundi][/usr/local/var/elasticsearch/elasticsearch_aironman/nodes/0] free: 33.6gb[7.2%], shards will be relocated away from this node
    ...

Execute kibana, as simple as put kibana in a terminal tag:

    $:clasificacion_tweets aironman$ kibana
    log   [17:14:59.962] [info][status][plugin:kibana] Status changed from uninitialized to green - Ready
    log   [17:14:59.988] [info][status][plugin:elasticsearch] Status changed from uninitialized to yellow - Waiting for Elasticsearch
    log   [17:15:00.001] [info][status][plugin:kbn_vislib_vis_types] Status changed from uninitialized to green - Ready
    log   [17:15:00.006] [info][status][plugin:markdown_vis] Status changed from uninitialized to green - Ready
    log   [17:15:00.014] [info][status][plugin:metric_vis] Status changed from uninitialized to green - Ready
    log   [17:15:00.019] [info][status][plugin:spyModes] Status changed from uninitialized to green - Ready
    log   [17:15:00.023] [info][status][plugin:statusPage] Status changed from uninitialized to green - Ready
    log   [17:15:00.027] [info][status][plugin:table_vis] Status changed from uninitialized to green - Ready
    log   [17:15:00.040] [info][listening] Server running at http://0.0.0.0:5601
    log   [17:15:00.046] [info][status][plugin:elasticsearch] Status changed from yellow to green - Kibana index ready

In order to run the job, you have to clone the project and compile it with sbt as usual.

    git clone https://github.com/alonsoir/twitter-sentiment-analysis.git

    cd twitter-sentiment-analysis

    sbt clean package assembly 


    $HOME/spark-1.5.2/bin/spark-submit \
        --class com.github.vspiewak.TwitterSentimentAnalysis \
        --master local[2] \
        target/scala-2.10/twitter-sentiment-analysis-assembly-0.1-SNAPSHOT.jar \
        <consumer_key> \
        <consumer_secret> \
        <access_token> \
        <access_token_secret> \
        [<filters>]

ie:

Do you want to know what is going on with FARC?

    /spark-1.5.2/bin/spark-submit \
        --class com.github.vspiewak.TwitterSentimentAnalysis \
        --master local[2] \
        target/scala-2.10/twitter-sentiment-analysis-assembly-0.1-SNAPSHOT.jar \
        <consumer_key> \
        <consumer_secret> \
        <access_token> \
        <access_token_secret> \
        FARC


Docker setup (To test!)
------------

Configure Twitter OAuth in [`docker-compose.yml`](./docker-compose.yml) file.

After installing docker & docker-compose, you can launch the stack with the following commands:

    docker-compose build
    docker-compose up


After a while, Elasticsearch, Kibana, and Spark should be up:

 * http://0.0.0.0:4040/streaming
 * http://0.0.0.0:9200/_plugin/head
 * http://0.0.0.0:5601
