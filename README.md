#

## Design and Development
### Light Lookup
- Light lookup is still very much a work in progress. I have been working on it alone for about a month and a half. It is still missing a lot of functionality: databases haven’t been populated and is still missing user registration and many other filtering methods.


## Technical Challenges

### Light Lookup - Bulk Upload (Server side)
- For Light Lookup, a technical challenge I had was the ability to upload multiple products to the database.
- It is possible to upload single products one at a time to the database using the website form. The intention is for manufacturers to be able to upload one-off products to an already established catalogue. However, for adding a large new catalogue to the database, this would be impractical and slow. 
- To achieve this, I created a new API endpoint `/populate` on the server, which accepts a csv file upload.

```js
// adminRouter.js
const express = require("express");
const multer = require("multer");
const upload = multer({ dest: "./" });

const adminController =  require("./../controllers/adminController");
const router  =  express.Router();

router.post("/populate", upload.single("file"), adminController.populate);

module.exports = router;
```
```js
//parseCsv.js
const csv = require("csv-parser");
const fs = require("fs");
const sanitise = require("./sanitise");

const parseCsv = (file) => {
  return new Promise((resolve, reject) => {
    const results = [];
    fs.createReadStream(file)
      .pipe(csv({}))
      .on("data", (product) => results.push(product))
      .on("end", () => {
        fs.unlinkSync(file);
        resolve(sanitise(results));
      });
  });
};

module.exports = parseCsv;
```

- The csv file is parsed through the above function and csv-parser module. This turns the csv file into an array of objects. Each document is sent through a `sanitise` function which checks whether every property is valid, and formats any nested lists correctly.

- Going forward, the UI for accessing this functionality still needs to be built, as it's only available through an API POST request. The route will also need to check if a particular user is authorised to carry out the request.


### Light Lookup - Interactive UI (Client side)
- I wanted the dashboard to be visual and fun for the user, bearing in mind the app is primarily targeted at designers. I wanted any changes to the form to be reflected in real-time, or have some kind of effect on a visual aspect of the form.
- This lead me to learn about inline svgs in React and their ability to dynamically render. For product beam angles, this involved hooking up a slider's (from MaterialUI/ React component library) controlled input to state, and then plugging this value directly into the "svg component", which accepts the value as a prop. 
- The component itself has a function which calculates the correct angle for each of the masks, creating the desired effect.

<!--INSERT IMAGE-->
