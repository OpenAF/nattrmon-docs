# Adding a new plug object to nAttrMon

In nAttrMon you have input, output and validation plugs. Althought theses plugs can have their own code the best pratice is to use the input, output and validation plug definition to use a specific existing object plug and then simply provide the necessary arguments. For example, to run a command and use the output as a monitoring metric you just need to define an input plug like this:

````yaml
input:
  name    : Number of files in /tmp
  execFrom: nInput_Shell
  execArgs:
    attrTemplate: Metrics/Number of files in tmp
    cmd         : |
      find /tmp | wc -l | tr -d '\n'
    jsonParse   : false
````

And you can quickly test it using the provided _nAttrMon_single.yaml.sample_ and replacing the sample _input_ with this one. Executing you will get:

````bash
$ ojob test.yaml
[...]
VAL | (name: 'Metrics/Number of files in tmp', val: '18', date: 2021-01-02/03:04:05.678)
[...]
````

You just used the _nInput_Shell_ object with the arguments _attrTemplate_, _cmd_ and _jsonParse_. nAttrMon comes with generic objects and more can also be added (check the [nAttrMon configs project](https://github.com/OpenAF/nattrmon-configs)).

So how do you build an object?

## Building a nInput object

Let's take an example and build a nInput object to retrieve details about HTTPs SSL certificates. Every HTTPs site as a SSL certificate and they come with a start and end date. Once the end date is reached the SSL certificate becomes invalid and browsers will prompt users an error whenever they try to access the site.

The simplest way to start is by using the provided _util/templates/Object_nInput_template.js_ code. Copy it to your _config/objects_ folder and rename it appropriately. 

### Using the template

Using the sample code provided in [the OpenAF "Check SSL Certificates" guide](https://docs.openaf.io/docs/guides/beginner/check-ssl-certificates.html) lets build a simple nInput_SSLCerts objects.

1. Create a nInput_SSLCerts.js files on a _config/objects_ folder
2. Copy the code from _util/templates/Object_nInput_template.js_
3. Rename all occurrences of _nInput_SomeObject_ by _nInput_SSLCerts_

### Define the input arguments

For our sample we want to be able to provide the site URL or address and port.

## Building a nOutput object

_tbc_