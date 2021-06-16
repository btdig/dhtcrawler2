## dhtcrawler2

dhtcrawler is a DHT crawler written in erlang. It can join a DHT network and crawl many P2P torrents. The program saves all torrent info into database and provide an http interface to search a torrent by a keyword.

![screenshot](https://raw.github.com/kevinlynx/dhtcrawler/master/screenshot.png)

dhtcrawler2 is an extended version to [dhtcrawler](https://github.com/kevinlynx/dhtcrawler). It has improved a lot on crawling speed, and is much more stable. 

This git branch maintains pre-compiled erlang files to start dhtcrawler2 directly. So you don't need to compile it yourself, just download it and run it to collect torrents and search a torrent by a keyword. 

Enjoy it!

## Usage

* install Erlang R16B or newer
* download mongodb and start mongodb first

        mongod --dbpath your-database-path --setParameter textSearchEnabled=true

* start **crawler**, on Windows, just click `win_start_crawler.bat`
* start **hash_reader**, on Windows, just click `win_start_hash.bat`
* start **httpd**, on Windows, just click `win_start_http.bat`
* wait several minutes and checkout `localhost:8000`

You can also compile the source code and run it manually. The source code is in `src` branch of this repo.

Also you can check more technique information at my blog site (Chinese) [codemacro.com](http://codemacro.com)

## Source code

dhtcrawler is totally open source, and can be used for any purpose, but you should keep my name on, copyright by me please. You can checkout dhtcrawler2 source code in this git repo **src** branch.

## Config

Most config value is in `priv/dhtcrawler.config`, when you first run dhtcrawler, this file will be generated automatically. And the other config values are passed by arguments to erlang functions. In most case you don't need to change these config values, except these network addresses.

## Mongodb Replica set

It's not related to dhtcrawler, but only Mongodb, try figure it yourself.

## Another http front-end

Yes of course you can write another http front-end UI based on the torrent database, if you're interested in it I can help you about the database format.

## Sphinx

Yes, dhtcrawler2 supports **sphinx** search. There's a tool named `sphinx-builder` load torrents from database and create sphinx index. `crawler-http` can also search text by sphinx. 

dhtcrawler2 uses mongodb text search by default, to use sphinx, follow these steps below:

* Download sphinx, the version tested is a fork version named `coreseek` which supports Chinese characters. [coreseek4.1](http://www.coreseek.cn/news/14/52/)
* unzip the binary archive and add `bin` directory to `PATH` environment variable, so that dhtcrawler can invoke `indexer` tool
* config `etc/csft.conf` file
    * add a delta index, i.e:
        
            source delta:xml
            {
                type = xmlpipe2
                xmlpipe_command = cat g:/downloads/coreseek-4.1-win32/var/test/delta.xml
            }
            index delta:xml
            {
                source = delta
                path = g:/downloads/coreseek-4.1-win32/var/data/delta
            }

    * change the other directories, better to use absolute path
* run `win_init_sphinx_index.bat` to generate a default sphinx-builder config file, and terminate `win_init_sphinx_index.bat`
* config `priv/sphinx_builder.config`, specify `main` and `delta` sphinx index source file name, `main` and `delta` index name and sphinx config file, these file names must match these configs you write in `etc/csft.conf`
* run `win_init_sphinx_index.bat` again to initialize sphinx index file, terminate `win_init_sphinx_index.bat` and if it initializes sphinx index successfully, never run it again
* run sphinx `searchd` server
* run `win_start_sphinx_builder` to start sphinx-builder, it will read torrents from your torrent database and build the index into sphinx
* change `priv/hash_reader.config` `search_method` to `sphinx`, so that `hash_reader` will not build mongodb text search index any more
* change `priv/httpd.config` `search_method` to `sphinx`, so that `crawler-http` will search keyword by sphinx

Lots of details! And you'd better to know sphinx well. 

## LICENSE

See LICENSE.txt
