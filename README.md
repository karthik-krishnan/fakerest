# Fakerest
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/karthik-krishnan/fakerest?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Fakerest is a simple tool based on sinatra which starts a http server (webrick) and exposes restful services based on the configuration specified in a YAML format.

[![Build Status](https://travis-ci.org/katta/fakerest.png)](https://travis-ci.org/katta/fakerest)


## Features

You can :

* Stub any of the restful servies be it any method GET, POST, HEAD, PUT etc.
* Configure multiple URLs based on your need
* Define the response code for each of the URL
* Define the response content type for each of the URL
* Define the response content based on `erb` templates for each of the URL
* Look at the requests made by your application for verifying what the application request look like. Quite handly for testing.
* Upload files and view the files uploaded in your stub.

## Prerequisites

Ruby version `1.9.3`

These are required libraries to build and install fakerest:

* [sinatra](https://github.com/sinatra/sinatra) - webrick based library to host rest services
* [rake](https://github.com/jimweirich/rake) - build tool for ruby (required only if you are building from code)
* [mocha](https://github.com/freerange/mocha) - mocha is a mocking and stubbing library for Ruby (this is required only for running tests)

## Installing

```bash
gem install fakerest
```

## Building from code

Follow these simple steps to create a gem and install it

```bash
git clone git://github.com/katta/fakerest.git
cd fakerest
bundle install
rake package
gem install pkg/fakerest-<version>.gem
```
    
Once you install this gem it creates an executable `fakerest` in the gems default executable directory. To find the gems executable directory use the command `gem environment` and look out for _EXECUTABLE DIRECTORY:_

For easy access update your `PATH` environment variable to point to a _EXECUTABLE DIRECTORY:_

## Usage

You can run fakerest using a command

```bash
$ fakerest
```

this will display the instructions on all the command line arguments to use fakerest which looks like 

    Usage: fakerest.rb [options]
     -c, --config CONFIG_FILE         Confilg file to load request mappings (required)
     -p, --port PORT                  Port on which the fakerest to be run. Default = 1111
     -o, --bind HOSTNAME/IP_ADDR      String specifying the hostname or IP address of the interface to listen on . Default = localhost
     -w, --views VIEWS_FOLDER         Folder path that contains the views. Default = <WORKING_DIR>/views
     -u, --uploads UPLOADS_FOLDER     Folder to which any file uploads to be stored. Default = <WORKING_DIR>/uploads
     -h, --help                       Displays help message
    
### Examples

#### Specifying config file

```bash
$ fakerest -c sample.yaml
```
    
`sample.yaml` is a config file which contains the configuration of all rest services you would like to host. It looks like 

```yml
---
method : get
path : /customer/:id
response:
  content_file : customer
  content_type : json
  status_code : 200
---
method : post
path : /customer
response:
  content_file : customer_created
  content_type : text/plain
  status_code : 200
```

#### Changing the default port

Use `-p` option to change the port on which the fakerest runs. By default it runs on `1111` port.

```bash
$ fakerest -p 2222 -c sample.yaml
```

### Views

Views are the `erb` template files from which the content is read and is served as a response to a http request based on the view file specified in the configuration.

For e.g. In the following configuration, notice the value for `content_file`. Fakerest looks for a template `customer.erb` in the views folder.
```yml
method : get
path : /customer/:id
response:
  content_file : customer # view file
  content_type : json 
  status_code : 200
```
Option `-w` can be used to tell Fakerest the folder in which the view files are held. A samile view file will look like this

```erb
{
  "id" : "<%= params["id"] %>",
  "name" : "John"
}
```

Notice the expression `<%= params["id"] %>` in the above template code, this will get evaluated before the response is served to the client by Fakerest using the parameters passed in the request.

In the above case if a request is made to a url `http://localhost:1111/customer/20` the response will be 

```json
{
  "id" : "20",
  "name" : "John"
}
```

### File uploads

You can do a post with file attachments to fakerest server and verify the content of the file using the browser (see next section)

> There is a known issue with this, the file attachment parameter name should be "file"

### Verifying the requests made to fakerest

Just go to your favorite browser and hit `http://localhost:1111/requests/10` where `10` is a number of recent requests you would like to verify. You could change that number `10` to number of requests you are interested in.

### Released versions

* `0.0.6` - option to bind to a given IP Address
* `0.0.4` - minor bug fixes
* `0.0.3` - with fix for file uploads
* `0.0.2` - initial version (with known issue of file uploads not working)
