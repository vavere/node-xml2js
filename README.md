xml2js-parser
=============

Simple XML to JavaScript object converter that uses a [sax-js](https://github.com/isaacs/sax-js/) for parsing.
Support async as native promise or callback and sync mode operation.

Description
-----------

This is a fork from a wonderful [xml2js](https://www.npmjs.com/package/xml2js) module. 

It aims to keep backwards compatibility with `xml2js` version 0.4.17 and 
target `nodejs` v6.x but without XML building support.

Motivation behind fork was:
- do not use `coffee-script`
- do one thing and do it well (_no builder_)
- minimal dependency tree (_no lodash_)
- use javascript new features from node v6.x

Installation
-----------

Yo install just type `npm install xml2js-parser` which will download xml2js-parser and dependencies.

Shoot-and-forget usage
----------------------

You want to parse XML as simple and easy as possible? Take this:

```javascript
const parseString = require('xml2js-parser').parseString;
const xml = '<root>Hello xml2js-parser!</root>';
parseString(xml, (err, result) => {
  console.dir(result);
});
```

If you need some special options, `xml2js-parser` supports a number of
options (see below), you can specify these as second argument:

```javascript
parseString(xml, {trim: true}, function (err, result) => {
  // processed data
});
```

New promises usage
------------------

```javascript
const parseString = require('xml2js-parser').parseString;

const xml = '<root>Hello xml2js-parser!</root>';
parseString(xml)
.then(res) => console.log(res))
.catch(err) => console.error(err));
});
```

Standard usage
--------------

```javascript
const fs = require('fs');
const Parser = require('xml2js-parser');

var parser = new Parser({trim: true});
fs.readFile(__dirname + '/foo.xml', (err, xml) => {
  parser.parseString(xml, (err, result) => {
    console.dir(result);
  });
});
```

Sync mode
---------

```javascript
const fs = require('fs');
const parseStringSync = require('xml2js-parser').parseStringSync;

var parser = new Parser({trim: true});
fs.readFile(__dirname + '/foo.xml', (err, xml) => {
  try {
    const res = parser.parseStringSync(xml);
    console.log(res);
  } catch (err)  {
    console.error(err);
  }
});
```

Processing attribute, tag names and values
------------------------------------------

You can optionally provide the parser with attribute name and tag name processors as well 
as element value processors:

```javascript
function nameToUpperCase(name) {
  return name.toUpperCase();
}

// transform all attribute and tag names and values to uppercase
parseString(xml, {
  tagNameProcessors: [nameToUpperCase],
  attrNameProcessors: [nameToUpperCase],
  valueProcessors: [nameToUpperCase],
  attrValueProcessors: [nameToUpperCase]},
  (err, result) => {
    // processed data
});
```

The `tagNameProcessors`, `attrNameProcessors`, `attrValueProcessors` and `valueProcessors` options
accept an `Array` of functions with the following signature:

```javascript
function (name) { //do something with name and return result }
```

Some processors are provided out-of-the-box and can be found in code:

- `normalize`: transforms the name to lowercase.
(Automatically used when `normalize` option is set to `true`)

- `firstCharLowerCase`: transforms the first character to lower case.
E.g. 'MyTagName' becomes 'myTagName'

- `stripPrefix`: strips the xml namespace prefix. E.g `<foo:Bar/>` will become 'Bar'.
(N.B.: the `xmlns` prefix is NOT stripped.)

- `parseNumbers`: parses integer-like strings as integers and float-like strings as floats
E.g. "0" becomes 0 and "15.56" becomes 15.56

- `parseBooleans`: parses boolean-like strings to booleans
E.g. "true" becomes true and "False" becomes false

Options
-------

Apart from the default settings, there are a number of options that can be
specified for the parser. Options are specified by ``new Parser({optionName:
value})``. Possible options are:

  * `attrkey` (default: `$`): Prefix that is used to access the attributes.
  * `charkey` (default: `_`): Prefix that is used to access the character content. 
  * `explicitCharkey` (default: `false`)
  * `trim` (default: `false`): Trim the whitespace at the beginning and end of
    text nodes.
  * `normalizeTags` (default: `false`): Normalize all tag names to lowercase.
  * `normalize` (default: `false`): Trim whitespaces inside text nodes.
  * `explicitRoot` (default: `true`): Set this if you want to get the root
    node in the resulting object.
  * `emptyTag` (default: `''`): what will the value of empty nodes be.
  * `explicitArray` (default: `true`): Always put child nodes in an array if
    true; otherwise an array is created only if there is more than one.
  * `ignoreAttrs` (default: `false`): Ignore all XML attributes and only create
    text nodes.
  * `mergeAttrs` (default: `false`): Merge attributes and child elements as
    properties of the parent, instead of keying attributes off a child
    attribute object. This option is ignored if `ignoreAttrs` is `false`.
  * `validator` (default `null`): You can specify a callable that validates
    the resulting structure somehow, however you want. See unit tests
    for an example.
  * `xmlns` (default `false`): Give each element a field usually called '$ns'
    (the first character is the same as attrkey) that contains its local name
    and namespace URI.
  * `explicitChildren` (default `false`): Put child elements to separate
    property. Doesn't work with `mergeAttrs = true`. If element has no children
    then "children" won't be created. 
  * `childkey` (default `$$`): Prefix that is used to access child elements if
    `explicitChildren` is set to `true`. 
  * `preserveChildrenOrder` (default `false`): Modifies the behavior of
    `explicitChildren` so that the value of the "children" property becomes an
    ordered array. When this is `true`, every node will also get a `#name` field
    whose value will correspond to the XML nodeName, so that you may iterate
    the "children" array and still be able to determine node names. The named
    (and potentially unordered) properties are also retained in this
    configuration at the same level as the ordered "children" array. 
  * `charsAsChildren` (default `false`): Determines whether chars should be
    considered children if `explicitChildren` is on.
  * `includeWhiteChars` (default `false`): Determines whether whitespace-only
     text nodes should be included. 
  * `strict` (default `true`): Set sax-js to strict or non-strict parsing mode.
    Defaults to `true` which is *highly* recommended, since parsing HTML which
    is not well-formed XML might yield just about anything. 
  * `attrNameProcessors` (default: `null`): Allows the addition of attribute
    name processing functions. Accepts an `Array` of functions.
  * `attrValueProcessors` (default: `null`): Allows the addition of attribute
    value processing functions. Accepts an `Array` of functions.
  * `tagNameProcessors` (default: `null`): Allows the addition of tag name
    processing functions. Accepts an `Array` of functions.
  * `valueProcessors` (default: `null`): Allows the addition of element value
    processing functions. Accepts an `Array` of functions.

Getting support
---------------

If you know you really found a bug, feel free to open an issue.

Running tests, development
--------------------------

The development requirements are handled by `npm install` if you clone repository.
We also have a number of unit tests, they can be run using `npm test` directly
from the project root. This runs zap to discover all the tests and execute
them.

How to contribute
-----------------

I'm always happy about useful new pull requests. Keep in mind that the better
your pull request is, the easier it can be added to `xml2js-parser`. As such please
make sure your patch is ok:

 * `xml2js-parser` is written in ECMAScript 2015 for node v6.x, respect it.
 * Make sure that the unit tests still all pass. 
 * Please, add a unit test with your pull request, to show what was broken and
   is now fixed or what was impossible and now works due to your new code.
 * If you add a new feature, please add some documentation that it exists.

If you like, you can add yourself in the `package.json` as contributor if you
deem your contribution significant enough. 
