Step -1:
- Install few packeges: `npm i --save react-awesome @pathofdev/react-tag-input firebase react-owl carousel react-router-dom react-toastify`
- Before working with `files/images`, go to `firestore -> storage`, change rule from `false` to `true`

##### App.js

```js
import React, { useEffect, useState } from "react";
import { Route, Routes, useNavigate, Navigate } from "react-router-dom";
import { onAuthStateChanged, signOut } from "firebase/auth";
import { auth } from "./utility/firebase";
function App() {
  const [active, setActive] = useState("home");
  const [user, setUser] = useState(null);
  const navigate = useNavigate();

  const handleSignout = () => {
    signOut(auth).then(() => {
      setUser(null);
      setActive("auth");
      navigate("/auth");
    });
  };

  useEffect(() => {
    // Check user logged in or not
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

        <Route path="/posts">
          <Route index element={<Posts />} />
        //   set condition so that without auth users cannot see the option
          <Route path=":add-post" element={user?.uid ? <AddPost user={user} /> : <Navigate to="/" />}/>
          <Route path="edit-post/:postId" element={user?.uid ? <EditPost user={user} /> : <Navigate to="/" />}/>
        </Route>

        <Route path="/about" element={<About />} />
        <Route path="auth" element={<Authentication setActive={setActive} setUser={setUser} />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </div>
  );
}

export default App;
```

##### add-post.js
```js
import React, { useState, useEffect } from "react";
import ReactTagInput from "@pathofdev/react-tag-input";
import "@pathofdev/react-tag-input/build/index.css";
import { db, storage } from "../../utility/firebase";
import { useNavigate, useParams } from "react-router-dom";
import { getDownloadURL, ref, uploadBytesResumable } from "firebase/storage";
import {addDoc, collection, getDoc, serverTimestamp, doc, updateDoc} from "firebase/firestore";

const defaultFormFields = {
  title: "",
  tags: [],
  trending: "no",
  category: "",
  description: "",
  comments: [],
  likes: [],
};
const categoryOption = ["Fashion", "Technology", "Food", "Politics", "Sports", "Business"];

const AddPost = ({ user }) => {
  const [formFields, setFormFields] = useState(defaultFormFields);
  const [file, setFile] = useState(null);
  const [progress, setProgress] = useState(null);
  const navigate = useNavigate();
  const { title, tags, category, trending, description } = formFields;

  //   create a reset fields
  const resetFormFields = () => {
    setFormFields(defaultFormFields);
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (category && tags && title && description && trending) {
      try {
        await addDoc(collection(db, "blogs"), {
          ...formFields,
          timestamp: serverTimestamp(),
          author: user.displayName,
          userId: user.uid,
        });
        // toast.success("Blog created successfully");
      } catch (err) {
        console.log(err);
      }
    }
    navigate("/");
  };

  // Create a function for working with fields
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormFields({ ...formFields, [name]: value });
  };

  const handleTags = (tags) => {
    setFormFields({ ...formFields, tags });
  };

  // Upload image to firebase storate, we need to goto firebase then storage and change rule true
  useEffect(() => {
    const uploadFile = () => {
      const storageRef = ref(storage, file.name);
      const uploadTask = uploadBytesResumable(storageRef, file);
      uploadTask.on(
        "state_changed",
        (snapshot) => {
          const progress =
            (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
          console.log("Upload is " + progress + "% done");
          setProgress(progress);
          switch (snapshot.state) {
            case "paused":
              console.log("Upload is paused");
              break;
            case "running":
              console.log("Upload is running");
              break;
            default:
              break;
          }
        },
        (error) => {
          console.log(error);
        },
        () => {
          getDownloadURL(uploadTask.snapshot.ref).then((downloadUrl) => {
            // toast.info("Image upload to firebase successfully");
            setFormFields((prev) => ({ ...prev, imgUrl: downloadUrl }));
          });
        }
      );
    };
    file && uploadFile();
  }, [file]);

  return (
    <>
      <div className="container-fluid">
        <div className="row">
          <div className="col-md-8 offset-md-2">
            <h2>Add a new post</h2>
            <form onSubmit={handleSubmit}>
              <div className="mb-3">
                <input
                  type="text"
                  className="form-control"
                  required
                  onChange={handleChange}
                  name="title"
                  value={title}
                  placeholder="Title"
                ></input>
              </div>
              <div className="mb-3">
                <ReactTagInput
                  tags={tags}
                  placeholder="Tags"
                  onChange={handleTags}
                />
              </div>
              <div className="mb-3">
                <div className="form-check-inline mx-2">
                  <span className="trending">Is it trending blog?</span>{" "}
                  <input
                    type="radio"
                    className="form-check-input"
                    value="yes"
                    name="trending"
                    checked={trending === "yes"}
                    onChange={handleChange}
                  />
                  <label htmlFor="trending" className="form-check-label">
                    &nbsp; Yes &nbsp;
                  </label>
                  <input
                    type="radio"
                    className="form-check-input"
                    value="no"
                    name="trending"
                    checked={trending === "no"}
                    onChange={handleChange}
                  />
                  <label htmlFor="trending" className="form-check-label">
                    &nbsp; No &nbsp;
                  </label>
                </div>
              </div>
              <div className="mb-3">
                <div className="col-12 py-3">
                  <select
                    name="category"
                    value={category}
                    onChange={handleChange}
                    className="catg-dropdown form-control"
                  >
                    <option>Please select category</option>
                    {categoryOption.map((option, index) => (
                      <option value={option || ""} key={index}>
                        {option}
                      </option>
                    ))}
                  </select>
                </div>
              </div>
              <div className="mb-3">
                <textarea
                  className="form-control description-box"
                  placeholder="Description"
                  value={description}
                  name="description"
                  onChange={handleChange}
                  style={{ height: "150px" }}
                />
              </div>
              <div className="mb-3">
                <input
                  type="file"
                  className="form-control"
                  onChange={(e) => setFile(e.target.files[0])}
                />
              </div>
              <button
                type="submit"
                disabled={progress !== null && progress < 100}
                className="btn btn-primary"
              >
                Add Post
              </button>
            </form>
          </div>
        </div>
      </div>
    </>
  );
};

export default AddPost;
