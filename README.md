# pocket2kindle

- [pocket2kindle](#pocket2kindle)
  * [What does it do](#what-does-it-do)
  * [Requirements](#requirements)
    + [Docker requirements](#docker-requirements)
    + [Standalone requirements](#standalone-requirements)
  * [Getting access to Pocket API](#getting-access-to-pocket-api)
  * [Usage](#usage)
    + [Docker](#docker)
    + [Standalone](#standalone)
      - [Installation](#installation)
      - [Execution](#execution)
  * [FAQ](#faq)
    + [Why do I have to provide Pocket's username/password AND the API keys?](#why-do-i-have-to-provide-pocket-s-username-password-and-the-api-keys-)
    + [I have noticed that the Pocketplus recipe already archives the bookmarks after creating the ebook, why the API calls?](#i-have-noticed-that-the-pocketplus-recipe-already-archives-the-bookmarks-after-creating-the-ebook--why-the-api-calls-)
    + [I am a Feedly user, will you provide a Feedly2Kindle version?](#i-am-a-feedly-user--will-you-provide-a-feedly2kindle-version-)
  * [Changelog](#changelog)
    + [1.1.0](#110)
    + [1.0](#10)

Simple script that uses calibre to generate an ebook with the bookmarks stored in Pocket, sends it to the specified (kindle) email address and then archives all the sent bookmarks.

It is avalaible as a docker container, but you can download the code and run it anywhere.

## What does it do

This script:

* Uses Calibre to connect to your Pocket account and build an ebook with your news
* Uses Calibre to connect to an email server and send the ebook to your email.
* Uses the Pocket API to archive all the items

It has been tested in Docker and Linux (Ubuntu, Raspbian), but it should work in other operating systems.

## Requirements

* A Pocket account
* A Pocket CONSUMER_KEY and a valid ACCESS_TOKEN. Check 'Getting access to Pocket API' section.
* An email account accessible using SMTP. Gmail is OK.
* An email address to receive the ebook, if you have a kindle it will be downloaded OTA.

### Docker requirements

* Tested in Docker 1.12.1

### Standalone requirements

* Nodejs
* Calibre ( https://calibre-ebook.com/ )
* Pocket+ Calibre recipe, from Marcin Magnus ( https://github.com/mmagnus ). Already included.

## Getting access to Pocket API

To use this script you need access to Pocket's API, but Pocket doesn't provide developer tokens. Therefore, you have to use oath to generate an access tokens. It's simpler than it sounds, just follow this guide from James Mackenzie: 

http://www.jamesfmackenzie.com/getting-started-with-the-pocket-developer-api/

## Usage

### Docker

Pocket2kindle is avalaible in DockerHub. Just download the .env-sample file (https://raw.githubusercontent.com/alvaroreig/pocket2kindle/master/.env-sample) , input your credentials and run the container:

```sh
docker run --rm --env-file YOUR_ENV_FILE alvaroreig/pocket2kindle
```

The output will show you the progress:. With loglevel=info:
```sh
info:  Create ebook flag=true, Send ebook flag=true, Archive in Pocket flag=false
info:  List of tags=[\'TAG_1\',\'TAG_2\',...,\'TAG_N\']
info:  Starting ebook creation=...
info:  Finished ebook creation=OK
info:  Sending ebook to kindle=...
info:  Ebook sent=OK
```

### Standalone

#### Installation

Install nodejs and calibre. Asuming Ubuntu/Debian:

```sh
sudo apt-get install calibre nodejs
```

Clone the repository:

```sh
git clone
```

Copy the sample .env file and fill in your values in your .env file:

```sh
cd pocket2kindle
cp .env-sample .env
```

If you are sending the ebook to a kindle address, make sure that the email send-from address is in the approved list of your amazon account. Check https://www.amazon.com/gp/help/customer/display.html?nodeId=201974240

Set your options in the pocketplus.recipe file, starting in line 39:

    # Settings people change
    max_articles_per_feed = 200
    minimum_articles      = 1
    mark_as_read_after_dl = False # Doesn't work sometimes, that's why we are calling Pocket API afterwards
    sort_method           = 'newest'  # MUST be either 'oldest' or 'newest'
    # To filter by tag this needs to be a single tag in quotes; IE 'calibre'
    only_pull_tag         = None
    tags =  ['Expertos','Cultura','Deporte', 'AAPP','Tecnología' ] #Name of your tags in Pocket
    
Install the dependencies of the proyect

```sh
npm install
```



#### Execution

Inside the directory, execute:

```sh
npm start
```

Or directly call index.js:

```sh
node $REPOSITORY_DIRECTORY/index.js
```

If you want to receive your news every day, just set up a cronjob. In my raspberry pi the line is:

    # Every day at 6 AM
    00 6    * * *   /usr/local/bin/node /home/pi/scripts/pocket2kindle/index.js >> /home/pi/pocket.log

## FAQ

### Why do I have to provide Pocket's username/password AND the API keys?

The Calibre Recipe uses web scrapping, so they username/password is needed for logging in.

The API keys are needed to archive the bookmarks.

### I have noticed that the Pocketplus recipe already archives the bookmarks after creating the ebook, why the API calls?

The pocletplus recipe works great, but sometimes (in my case, pretty often) the archive operation fails. Therefore, a bulk archive is needed if you don't want to get duplicated content the next time.

### I am a Feedly user, will you provide a Feedly2Kindle version?

Actually I am a Feedly user. I chose Pocket because the hardest part of the work ( creating the ebook) was already avalaible. My "entry point" is Feedly, but it is easy to copy news from Feedly to Pocket using IFTTT:

https://ifttt.com/recipes/102176-feeds-to-pocket

Just set up a rule that maps your desired categories in Feedly with the corresponding tags in Pocket.

If you use any other service to access your favorite sites, just look for a similar rule in IFTTT.

## Changelog

### 1.1.0

* Included a parameter to specify the tags used in the ebook's creation process. These tags will overwrite the line 46 of the pocketplus.recipe file
* Small bugfixes

### 1.0

* Initial version.