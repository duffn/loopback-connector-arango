## loopback-connector-arango

ArangoDB connector for LoopBack.

- [Original author](https://github.com/nvdnkpr)
- [Original NPM registry](https://www.npmjs.com/package/loopback-connector-arango)

Modified by me as the Github repository and NPM registry were out of sync as of 5/8/15.

This code is pulled from NPM and updated with some changes to work with new versions of LoopBack.

Thanks for [nvdnkpr](https://github.com/nvdnkpr) for the original code.

Original README below.

### ArangoDB related customization
#### _id, _key and _rev
- Every ArangoDB doc comes with three properties:
  - *_key* which is the document's id collection-wide
  - *_id* which is the document's id database-wide. It's format is `COLLECTIONNAME/_key`
  - *_rev* the current revision of this document. If a document changes between the retrieval and the update of a document, the revision's of this two (different) documents will mismatch.

The *_id* property format `Person/123456789` with its middle `/` is not very URL-friendly as ID, especially for REST HTTP routes like `/persons/{personId}` (would generate `/persons/Person/123456789`, uuurgh).

However the *_key* property fits very well, since it would generate a nice looking URL like `/persons/123456789`. Unfortunately most of the arangojs clients methods expect the *_id* property.

Additionally to that every document contains the triple of `_id`, `_key` and `_rev` which makes our documents very verbose.

#### ArangoDB Datatypes: null, Boolean, Number, String, Array, Document vs. Loopback DataTypes like GeoPoint  and Date
Like every other connector we also need to convert loopback data types to Arango Datatypes and vice versa.

#### Solution: fromDB and toDB
To solve the problem with

ArangoDB has two properties containing the IDs:

  - The collection are named after the models
  - Every
  - We use the (collection-wide) *_key* as the `id` for our responses and drop the *_key* property from the results
  - The unique (database-wide) *_id* property which is composed of the collection name and the *_key* property

The *_id* is not very URL-friendly for APIs, the *_key* would fit better. So the best for us would be to work with the *_key* property as id and ignore the *_id*.
Unfortunately most of the `arangojs` client methods need the *_id* property.



So in order to solve this problem this Connector has two additional methods:

   - `fromDB`

But since most of the methods of the arangojs client (which is just a http request wrapper for the http based Arango REST API) need the the *_id* property (called the "document handle") we use the *_key* in the returned results and compose ourselves an given *_key* by using a helper function `composeId` which simply takes a model/collection name and a id and combines them to the *_id* property.



The connector solves this by using providing a `generateId` method which needs the `model` and a

Since the loopback connectors querying methods always have a `model` parameter, if an id is provided both is run through a `generateId` method that generates us


## Usage

To use it you need `loopback-datasource-juggler@1.0.x`.

1. Setup dependencies in `package.json`:

    ```json
    {
      ...
      "dependencies": {
        "loopback-datasource-juggler": "~1.0.0",
        "loopback-connector-arango": "latest"
      },
      ...
    }
    ```

2. Use:

    ```javascript
        var DataSource = require('loopback-datasource-juggler').DataSource;
        var ds = new DataSource('redis');
    ```

## Running tests

Tests are currently broke since it was a port from the jugglingDB Adapter for Arango.
I'll have to adapt the tests

But it will be the normal

    npm test

## MIT License

    Copyright (C) 2012 by Navid Nikpour

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in
    all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
    THE SOFTWARE.

