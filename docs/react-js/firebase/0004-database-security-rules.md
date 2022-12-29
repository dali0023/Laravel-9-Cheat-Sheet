# Understand Firebase Realtime Database Security Rules:

These Rules determine who has read and write access to your database, how your data is structured, and what indexes exist. These rules live on the Firebase servers and are enforced automatically at all times. Every read and write request will only be completed if your rules allow it. By default, your rules do not allow anyone access to your database. This is to protect your database from abuse until you have time to customize your rules or set up authentication.

###### Realtime Database Security Rules have a JavaScript-like syntax and come in four types:

- `.read` Describes if and when data is allowed to be read by users.
- `.write` Describes if and when data is allowed to be written.
- `.validate` Defines what a correctly formatted value will look like, whether it has child attributes, and the data type.
- `.indexOn` Specifies a child to index to support ordering and querying.

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /{document=**} {
      allow read, write: if true;
    }
    // posts
     match /posts/add-post {
       allow read: if request.auth.uid != null; // only all logged in users can read it
    //    allow write: if request.auth.token.admin == true; // only admin can write it
       allow write: if true;
     }
  }
}
```
