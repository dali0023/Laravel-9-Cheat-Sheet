# React Js Authentication
###### Step -1:  
- Before using Firebase, install it: `npm i firebase `

###### Step -2:
 - Create a new file, `firebase.js`, and initialize Firebase:
```js
// import the Firebase modules 
import { initializeApp } from "firebase/app";
import React from "react";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut} from "firebase/auth";
import{getFirestore, doc, getDoc, getDocs, setDoc, collection, writeBatch, query} from "firebase/firestore";

// Integrating Firebase into our React app
const firebaseConfig = {
  apiKey: <api_key>,
  authDomain:<auth_domain> ,
  projectId: <project_id>,
  storageBucket: <storage_bucket>,
  messagingSenderId: <messaging_sender_id>,
  appId: <app_id>
};

// Let’s initialize our app and services so that we can use Firebase throughout our app:
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
```
###### Step -3: 
- We will be creating all important authentication-related functions in `firebase.js`

`Register With Email And Password:`
```js
const registerWithEmailAndPassword = async (name, email, password) => {
  try {
    const res = await createUserWithEmailAndPassword(auth, email, password);
    const user = res.user;
    await addDoc(collection(db, "users"), {
      uid: user.uid,
      name,
      authProvider: "local",
      email,
    });
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
```
`Signing in using an email and password:`

```js
const logInWithEmailAndPassword = async (email, password) => {
  try {
    await signInWithEmailAndPassword(auth, email, password);
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};

```
`Sign In With Google:`

```js
const googleProvider = new GoogleAuthProvider();
const signInWithGoogle = async () => {
  try {
    const res = await signInWithPopup(auth, googleProvider);
    const user = res.user;
    const q = query(collection(db, "users"), where("uid", "==", user.uid));
    const docs = await getDocs(q);
    if (docs.docs.length === 0) {
      await addDoc(collection(db, "users"), {
        uid: user.uid,
        name: user.displayName,
        authProvider: "google",
        email: user.email,
      });
    }
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
```
`Password Reset Link to an Email Address:`
```js
const sendPasswordReset = async (email) => {
  try {
    await sendPasswordResetEmail(auth, email);
    alert("Password reset link sent!");
  } catch (err) {
    console.error(err);
    alert(err.message);
  }
};
```
`Logout function:`
```js
const signOut = async () => {
    try {
      await signOut(auth);
      return true;
    } catch (error) {
      return false;
    }
  };
```