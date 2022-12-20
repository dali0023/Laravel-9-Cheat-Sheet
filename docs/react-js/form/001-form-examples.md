###### app.js

```js
import React, { useEffect, useState } from "react";
import { Route, Routes, useNavigate } from "react-router-dom";
import { onAuthStateChanged, signOut } from "firebase/auth";
import { auth } from "./utility/firebase";
function App() {
  const [active, setActive] = useState("home");  // add active class to menu
  const [user, setUser] = useState(null); // set user
  const navigate = useNavigate();   // add navigate funtion to redirect one to another page

  const handleSignout = () => {
    signOut(auth).then(() => {
      setUser(null);
      setActive("auth");
      navigate("/auth");
    });
  };

  useEffect(() => {
    onAuthStateChanged(auth, (user) => {
      if (user) {
        setUser(user);
      } else {
        setUser(null);
      }
    });
  }, []);
  return (
    <div className="App">
      <Header setActive={setActive} active={active} user={user} handleSignout={handleSignout} />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/details/:id" element={<Details />} />
        <Route path="/create" element={<AddEditBlog />} />
        <Route path="/update/:id" element={<AddEditBlog />} />
        <Route path="/about" element={<About />} />
        <Route
          path="auth"
          element={<Authentication setActive={setActive} setUser={setUser} />}
        />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </div>
  );
}

export default App;
```

###### authentication.js

```js
import React from "react";

import SignInForm from "../../components/sign-in-form/sign-in-form";
import "./Authentication.styels.scss";

const Authentication = ({ setActive, setUser }) => {
  return <SignInForm setActive={setActive} setUser={setUser} />;
};

export default Authentication;
```

###### header.js

```js
import React from "react";
import { Link } from "react-router-dom";
const Header = ({ active, setActive, user, handleSignout }) => {
  const userId = user?.uid;
  return (
    <>
      <ul>
        <Link to="/">
          <li className={`${active === "home" ? "active" : ""}`}onClick={() => setActive("home")}>Home</li>
        </Link>
        <Link to="/create">
          <li className={`${active === "create" ? "active" : ""}`}onClick={() => setActive("create")}>Create</li>
        </Link>
        <Link to="/about" style={{ textDecoration: "none" }}>
          <li className={`${active === "about" ? "active" : ""}`}onClick={() => setActive("about")}> About</li>
        </Link>
      </ul>

      <ul>
        {userId ? (
          <>
            <li>
              <img src="https://cdn-icons-png.flaticon.com/512/149/149071.png" alt="logo" />
            </li>
            <p>{user?.displayName}</p>
            <li onClick={handleSignout} > Sign Out </li>
          </>
        ) : (
          <Link to="/auth" style={{ textDecoration: "none" }}>
            <li className={`${active === "auth" ? "active" : ""}`} onClick={() => setActive("auth")}>Login</li>
          </Link>
        )}
      </ul>
  );
};

export default Header;
```
###### sign-in-form.js
```js
import React, { useState } from "react";
import { auth, signInWithEmailAndPassword } from "../../utility/firebase";
import SignUpForm from "../sign-up-form/sign-up-form";
import { useNavigate } from "react-router-dom";

const defaultFormFields = {
  email: "",
  password: "",
};

const SignInForm = ({setActive, setUser}) => {
  const navigate = useNavigate();
  const [formFields, setFormFields] = useState(defaultFormFields);
  const { email, password } = formFields;
  const [signIn, setSignIn] = useState(true);

  // set variables for display errors
  const [errorEmailMessege, setErrorEmailMessege] = useState(null);
  const [errorPasswordMessege, setErrorPasswordMessege] = useState(null);

  //   create a reset fields
  const resetFormFields = () => {
    setFormFields(defaultFormFields);
  };

  // working with form data
  const handleSubmit = async (event) => {
    event.preventDefault();

    await signInWithEmailAndPassword(auth, email, password)
      .then((response) => {
        resetFormFields();
        setActive('home');
        navigate("/");
      })
      .catch((error) => {
        switch (error.code) {
          case "auth/wrong-password":
            setErrorPasswordMessege("Incorrect password for email");
            break;
          case "auth/user-not-found":
            setErrorEmailMessege("No user associated with this email");
            break;
          default:
            alert("uncounted errors");
        }
      });
  };

  // Create a function for working with fields
  const handleChange = (event) => {
    const { name, value } = event.target;
    setFormFields({ ...formFields, [name]: value });
  };

  return (
    {signIn ? (
          <>
            <h2 className="text-center">Sign In</h2>
            <form onSubmit={handleSubmit}>
                <input type="email" className="form-control" required onChange={handleChange} name="email" value={email} ></input>
                {errorEmailMessege !== null && (<span className="text-danger">{errorEmailMessege}</span>)}

                <input type="password" className="form-control" required onChange={handleChange} name="password" value={password} ></input>
                {errorPasswordMessege !== null && ( <span className="text-danger">{errorPasswordMessege}</span>)}

              <button type="submit" className="btn btn-primary"> Sign In </button>
              <p onClick={() => setSignIn(false)} className="pointer"> Don't have an account? </p>
            </form>
          </>
        ) : (
          <>
            <SignUpForm setActive={setActive} setUser={setUser}/>
            <p onClick={() => setSignIn(true)} className="pointer"> Do you already have an account?</p>
          </>
        )}
  );
};

export default SignInForm;
```

# sign-up-form.js

```js
import React, { useState } from "react";
import {auth, createUserWithEmailAndPassword, updateProfile} from "../../utility/firebase";
import { useNavigate } from "react-router-dom";
const defaultFormFields = {displayName: "", email: "", password: "", confirmPassword: ""};

const SignUpForm = ({ setActive, setUser }) => {
  // console.log(setUser);
  const navigate = useNavigate();
  const [formFields, setFormFields] = useState(defaultFormFields);
  const { displayName, email, password, confirmPassword } = formFields;
  const [errorMessege, setErrorMessege] = useState(null);

  //   create a reset fields
  const resetFormFields = () => {
    setFormFields(defaultFormFields);
  };

  const handleSubmit = async (event) => {
    event.preventDefault();

    if (password !== confirmPassword) {
      setErrorMessege("passwords do not match");
      return;
    }

    await createUserWithEmailAndPassword(auth, email, password)
      .then((response) => {
        updateProfile(response.user, { displayName }).then(() => {
          // we must reload user when update user info so that user's info can display after sign up
          auth.currentUser.reload();
          setUser(auth.currentUser);
          setActive("home");
          navigate("/");
        });
      })
      .catch((error) => {
        console.log(error);
      });
  };
  // Create a function for working with fields
  const handleChange = (event) => {
    const { name, value } = event.target;
    setFormFields({ ...formFields, [name]: value });
  };

  return (
    <>
      <h2>Sign Up</h2>
      <form onSubmit={handleSubmit}>
          <input type="text" className="form-control" required onChange={handleChange} name="displayName" value={displayName}></input> 
          <input type="email" className="form-control" required onChange={handleChange} name="email" value={email}></input>
          
          <input type="password" className="form-control" required onChange={handleChange} name="password" value={password}></input>
          {errorMessege !== null && (<span className="text-danger">{errorMessege}</span>)}
          
          <input type="password" required onChange={handleChange} name="confirmPassword" value={confirmPassword} className="form-control"></input> 
          <button type="submit" className="btn btn-primary"> Sign Up</button>
      </form>
    </>
  );
};

export default SignUpForm;
```

###### firebase.js
```js
import { initializeApp } from "firebase/app";
import React from "react";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut, updateProfile} from "firebase/auth";
import {getFirestore, doc, getDoc, getDocs, setDoc, collection, writeBatch, query} from "firebase/firestore";
import { getStorage } from "firebase/storage";

const firebaseConfig = {
  apiKey: "AIzaSyBAJzuTw3tJh2pKYRMJCoLIeMIw5Q85T0o",
  authDomain: "react-blog-25811.firebaseapp.com",
  projectId: "react-blog-25811",
  storageBucket: "react-blog-25811.appspot.com",
  messagingSenderId: "363967723034",
  appId: "1:363967723034:web:d21f82c4fc47bd334783a3",
};
// Let’s initialize our app and services so that we can use Firebase throughout our app:
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const storage = getStorage(app);


export {auth, db, storage, createUserWithEmailAndPassword,signInWithEmailAndPassword, updateProfile, doc, setDoc}