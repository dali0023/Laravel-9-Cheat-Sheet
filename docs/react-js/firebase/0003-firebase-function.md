# Cloud Function / Adding Claims:
- Set up Cloud Functions
- Write functions
- Test functions
- Deploy and monitor

# Steps:

try to use git or command prompt:

- `npm install firebase`
- `npm install -g firebase-tools`
- `firebase login`
- `firebase init functions`
  the select existing project whatever I want
  Select JavaScript

# We Already got a function folder in our project and write below code inside functions>index.js

```js
// The Cloud Functions for Firebase SDK to create Cloud Functions and set up triggers.
const functions = require("firebase-functions");

// The Firebase Admin SDK to access Firestore.
const admin = require("firebase-admin");
admin.initializeApp();
console.log(admin);

exports.addAdminRole = functions.https.onCall((data, context) => {
  // get user and add admin custom claim
  return admin
    .auth().getUserByEmail(data.email).then((user) => {
      return admin.auth().setCustomUserClaims(user.uid, {
        admin: true,
      });
    }).then(() => {
      return {
        message: `Success! ${data.email} has been made an admin.`,
      };
    }).catch((err) => {
      return err;
    });
});
```

# Deploy functions to a production environment:

- firebase deploy --only functions
###### all time run the command if we change anything inside index.js

# Go to firestore project and then check inside functions

# Firebase utils
Add below lines inside `firebase.js`
```js
// for functions
import {getFunctions, httpsCallable, connectFunctionsEmulator} from "firebase/functions";
// for functions
const functions = getFunctions(app);
export {functions}
```

# Create a page where user make and admin:
``` make-admin.js```
```js
import React, { useState } from "react";
import { getFunctions, httpsCallable } from "firebase/functions";
import { functions } from "../../utils/firebase";

const MakeAdmin = () => {
  const [email, setEmail] = useState(null);

  // working with form data
  const handleSubmit = async (event) => {
    event.preventDefault();
    
    // set user's an admin
    const addAdminRole = httpsCallable(functions, "addAdminRole");
    addAdminRole({ email: email.email }).then((result) => {
      console.log(result.data);
    });
  };

  // Create a function for working with fields
  const handleChange = (event) => {
    const { name, value } = event.target;
    setEmail({ ...email, [name]: value });
  };
  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} name="email"/>
      <button type="submit"">Make Admin </button>
    </form>
  );
};

export default MakeAdmin;
```
# App.js
```js
  useEffect(() => {
    onAuthStateChanged(auth, (user) => {
      if (user) {
        // check and set admin 
        user.getIdTokenResult().then((idTokenResult) => {
          user.admin = idTokenResult.claims.admin;
        });
        setUser(user);  // you will see admin property in user data
      } else {
        setUser(null);
      }
    });
  }, []);
```