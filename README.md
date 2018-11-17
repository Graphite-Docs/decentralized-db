## Decentralized DB
Decentralized DB is a storage wrapper that combines Blockstack storage, IPFS storage, and IndexedDB storage using TurtleDB. It is configurable on each storage request, allows for encryption using elliptic curve encryption, and provides a simple API.

*Motivation*  
While Blockstack's Gaia storage, IPFS, and TurtleDB storage are all great on their own, there are many times when all three might be used. There are also times with combinations of the three might be used. This storage wrapper makes that possible with simple functions:

`storeFile`  
`loadFile`   

### Getting Started

*Install*  
`npm install decentralized-db`  

*Import/Require*  
`import { storeFile, loadFile } from "decentralizedDB"`  
`const decentralizedDB = require("decentralized-db")`  

Once you've imported/required the package, you can start using the functions. Remember, if you require the package rather than importing each function, you'll need to prefix your calls with "decentralizedDB". The docs will not include this so as a quick example, if you're not importing, you would make a call like this: `decentralizedDB.storeFile(options)`.

### storeFile

When you're ready to update a file or a store a new one, you'll simple need to call `storeFile` with the following options:

-filename [String]
-encrypt [boolean]
-ipfs [boolean]
-sync [boolean]
-data [Object or String or Boolean or Array]

*Important Notes:*
The filename String needs to have a suffix of '.json', '.txt', or some other valid extension that can contain string data. This is because storing to Blockstack and IPFS requires a valid pathname. The filename will act as both the pathname and your identifier for local IndexedDB storage.

Example:

```
import { storeFile } from "decentralized-db";

const options = {};
options.filename = 'testfile.json';
options.encrypt = true;
options.data = "Rage against the dying of the light";
options.ipfs = true;
options.sync = true;

storeFile(options).then((data) => {
  console.log(data)
})
```

You'll notice that you can chain on a .then to grab the response from your storeFile call. This is important because if you are storing to IPFS, the multihash will be included in that response. Since the only way to reference that multihash again would be to store it somewhere, you will likely want to make another storeFile call and post that multihash to Blockstack's Gaia storage. Here's what that would look like:

```
import { storeFile } from "decentralized-db";

const options = {};
options.filename = 'testfile.json';
options.encrypt = true;
options.data = "Rage against the dying of the light";
options.ipfs = true;
options.sync = true;

storeFile(options).then((data) => {
  const gaiaPost = {};
  gaiaPost.fileName = multihash.json;
  gaiaPost.encrypt = true;
  gaiaPost.data = data;
  gaiaPost.ipfs = false;
  gaiaPost.sync = true;
  storeFile(gaiaPost)
})
```

Finally, the `sync` property of the options object in your storeFile call is the way you indicate whether or not you want to store to Blockstack & IPFS/Blockstack-Only or if you just want to store to your local storage. Using this property wisely will allow you to cut down on http request and hopefully improve performance of your app.

If you indicate `sync: false` but also include `ipfs: true`, your ipfs property will be ignored. 
