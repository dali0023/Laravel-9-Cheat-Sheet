# Working with Login:

###### Step -1: 

- Declare empty object for form fields, easy and short way: `sign-in-form.jsx`

```js
const defaultFormFields = {
  email: "",
  password: "",
};

const SignInForm = () => {
  const [formFields, setFormFields] = useState(defaultFormFields);
  const { email, password } = formFields;
  const [signIn, setSignIn] = useState(true); // display Sign In or Sign Up page

  //   create a reset function to reset fields
  const resetFormFields = () => {
    setFormFields(defaultFormFields);
  };

  // working with form data
  const handleSubmit = async (event) => {
    event.preventDefault();

    try {

      resetFormFields();
    } catch (error) {}
  };

  // Create a function for working with fields
  const handleChange = (event) => {
    const { name, value } = event.target;
    setFormFields({ ...formFields, [name]: value });
  };

  return (
    <div className="row">
      <div className="col-md-4 offset-md-4">
        {signIn ? (
          <>
            <h2 className="text-center">Sign In</h2>
            <form onSubmit={handleSubmit}>
              <div className="mb-3">
                <label className="form-label">Email</label>
                <input type="email" onChange={handleChange} name="email" value={email} className="form-control" required ></input>
              </div>
              <div className="mb-3">
                <label className="form-label">Email</label>
                <input type="password" onChange={handleChange} value={email} name='password' className="form-control" required ></input>
              </div>
              <button type="submit" className="btn btn-primary"> Sign In </button>
              <p onClick={() => setSignIn(false)} className="pointer"> Don't have an account? </p>
            </form>
          </>
        ) : (
          <>
            <SignUpForm />
            <p onClick={() => setSignIn(true)} className="pointer"> Do you already have an account? </p>
          </>
        )}
      </div>
    </div>
  );
};
```

If we want to use form validation, just modity this code inside `handleChange function`
```js
  const [errorMessege, setErrorMessege] = useState(null); 
  const handleChange = (event) => {
    const { name, value } = event.target;
    if (name === "displayName") {
       setErrorMessege("Name Not valid");
       return;
     } 
    else if (name === "email") { } 
    else if (name === "password") { } 
    else if (name === "confirmPassword") { }

    setFormFields({ ...formFields, [name]: value });
  };
```
Also add, 
```js
 <input type="email" onChange={handleChange} name="email" value={email} className="form-control" required ></input>
 {errorMessege !== null && <span className="text-danger">error</span>}