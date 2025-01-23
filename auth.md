# Firebase Authentication with React Examples

## 1. Creating a User (`createUserWithEmailAndPassword`)

**Function Structure**:

```javascript
createUserWithEmailAndPassword(auth, email, password);
```

**Description**:

- Creates a new user account using an email address and password.
- Returns a `Promise` that resolves with the user's credentials.

**React Example**:

```jsx
import { useState } from "react";
import { auth } from "./firebase";
import { createUserWithEmailAndPassword } from "firebase/auth";

function Register() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");

  const handleRegister = async (event) => {
    event.preventDefault();
    try {
      await createUserWithEmailAndPassword(auth, email, password);
      setMessage("User registered successfully!");
    } catch (error) {
      setMessage("Error: " + error.message);
    }
  };

  return (
    <div>
      <h2>Register</h2>
      <form onSubmit={handleRegister}>
        <div>
          <label>Email:</label>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
        </div>
        <div>
          <label>Password:</label>
          <input
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
        </div>
        <button type="submit">Register</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default Register;
```

---

## 2. Signing In a User (`signInWithEmailAndPassword`)

**Function Structure**:

```javascript
signInWithEmailAndPassword(auth, email, password);
```

**Description**:

- Signs in an existing user with their email and password.
- Returns a `Promise` that resolves with the user's credentials.

**React Example**:

```jsx
import { useState } from "react";
import { auth } from "./firebase";
import { signInWithEmailAndPassword } from "firebase/auth";

function Login() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");

  const handleLogin = async (event) => {
    event.preventDefault();
    try {
      await signInWithEmailAndPassword(auth, email, password);
      setMessage("Login successful!");
    } catch (error) {
      setMessage("Error: " + error.message);
    }
  };

  return (
    <div>
      <h2>Login</h2>
      <form onSubmit={handleLogin}>
        <div>
          <label>Email:</label>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
        </div>
        <div>
          <label>Password:</label>
          <input
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
        </div>
        <button type="submit">Login</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default Login;
```

---

## 3. Signing Out a User (`signOut`)

**Function Structure**:

```javascript
signOut(auth);
```

**Description**:

- Signs out the current user.
- Returns a `Promise` that resolves when the sign-out operation completes.

**React Example**:

```jsx
import { auth } from "./firebase";
import { signOut } from "firebase/auth";

function Logout() {
  const handleLogout = async () => {
    try {
      await signOut(auth);
      alert("Logged out successfully!");
    } catch (error) {
      alert("Error: " + error.message);
    }
  };

  return <button onClick={handleLogout}>Logout</button>;
}

export default Logout;
```

---

## 4. Monitoring Authentication State (`onAuthStateChanged`)

**Function Structure**:

```javascript
onAuthStateChanged(auth, callback);
```

**Description**:

- Listens for changes to the authentication state.
- Calls the callback function whenever the user signs in or out.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { auth } from "./firebase";
import { onAuthStateChanged } from "firebase/auth";

function AuthStateListener() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (currentUser) => {
      setUser(currentUser);
    });

    return () => unsubscribe(); // Cleanup subscription on unmount
  }, []);

  return (
    <div>
      {user ? <p>Welcome, {user.email}!</p> : <p>No user signed in.</p>}
    </div>
  );
}

export default AuthStateListener;
```

---

## 5. Sending a Password Reset Email (`sendPasswordResetEmail`)

**Function Structure**:

```javascript
sendPasswordResetEmail(auth, email);
```

**Description**:

- Sends a password reset email to the specified email address.
- Returns a `Promise` that resolves when the email is sent.

**React Example**:

```jsx
import { useState } from "react";
import { auth } from "./firebase";
import { sendPasswordResetEmail } from "firebase/auth";

function PasswordReset() {
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");

  const handlePasswordReset = async () => {
    try {
      await sendPasswordResetEmail(auth, email);
      setMessage("Password reset email sent!");
    } catch (error) {
      setMessage("Error: " + error.message);
    }
  };

  return (
    <div>
      <h2>Reset Password</h2>
      <input
        type="email"
        placeholder="Enter your email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <button onClick={handlePasswordReset}>Send Reset Email</button>
      <p>{message}</p>
    </div>
  );
}

export default PasswordReset;
```

---

## 6. Sending a Verification Email (`sendEmailVerification`)

**Function Structure**:

```javascript
sendEmailVerification(user);
```

**Description**:

- Sends an email verification link to the current user.
- Returns a `Promise` that resolves when the email is sent.

**React Example**:

```jsx
import { auth } from "./firebase";
import { sendEmailVerification } from "firebase/auth";

function VerifyEmail() {
  const handleVerifyEmail = async () => {
    try {
      if (auth.currentUser) {
        await sendEmailVerification(auth.currentUser);
        alert("Verification email sent!");
      } else {
        alert("No user is signed in.");
      }
    } catch (error) {
      alert("Error: " + error.message);
    }
  };

  return <button onClick={handleVerifyEmail}>Send Verification Email</button>;
}

export default VerifyEmail;
```

---

## 7. Updating User Profile (`updateProfile`)

**Function Structure**:

```javascript
updateProfile(user, { displayName, photoURL });
```

**Description**:

- Updates the current user's profile with a new `displayName` and/or `photoURL`.
- Requires the user to be signed in.
- Returns a `Promise` that resolves when the profile update is complete.

**React Example**:

```jsx
import { useState } from "react";
import { auth } from "./firebase";
import { updateProfile } from "firebase/auth";

function UpdateUserProfile() {
  const [displayName, setDisplayName] = useState("");
  const [photoURL, setPhotoURL] = useState("");
  const [message, setMessage] = useState("");

  const handleUpdateProfile = async (event) => {
    event.preventDefault();

    try {
      if (auth.currentUser) {
        await updateProfile(auth.currentUser, { displayName, photoURL });
        setMessage("Profile updated successfully!");
      } else {
        setMessage("No user is signed in.");
      }
    } catch (error) {
      setMessage("Error: " + error.message);
    }
  };

  return (
    <div>
      <h2>Update Profile</h2>
      <form onSubmit={handleUpdateProfile}>
        <div>
          <label htmlFor="displayName">Display Name:</label>
          <input
            type="text"
            id="displayName"
            value={displayName}
            onChange={(e) => setDisplayName(e.target.value)}
          />
        </div>
        <div>
          <label htmlFor="photoURL">Photo URL:</label>
          <input
            type="text"
            id="photoURL"
            value={photoURL}
            onChange={(e) => setPhotoURL(e.target.value)}
          />
        </div>
        <button type="submit">Update Profile</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default UpdateUserProfile;
```

---

**Steps to Implement**:

1. **Import Required Functions**: Import `updateProfile` from Firebase Authentication.
2. **Capture User Input**: Create form fields for `displayName` and `photoURL`.
3. **Check for Signed-in User**: Ensure `auth.currentUser` is not null before calling `updateProfile`.
4. **Handle Errors**: Use a `try-catch` block to manage errors effectively.
5. **Update UI**: Provide feedback to the user about the success or failure of the operation.

---

**Notes**:

- Both `displayName` and `photoURL` are optional parameters; you can update either one or both.
- The updated profile information is available in the `auth.currentUser` object after the update.
- Ensure proper validation of user inputs (e.g., URL validation for `photoURL`) before calling `updateProfile`.

---

## 8. Signing In with Popup (`signInWithPopup`)

**Function Structure**:

```javascript
signInWithPopup(auth, provider);
```

**Description**:

- Opens a popup window for signing in with a third-party provider (e.g., Google, Facebook).
- Returns a `Promise` that resolves with the user's credentials.

**React Example**:

```jsx
import { auth, googleProvider } from "./firebase";
import { signInWithPopup } from "firebase/auth";

function SignInWithPopup() {
  const handleSignIn = async () => {
    try {
      const result = await signInWithPopup(auth, googleProvider);
      console.log("User Info:", result.user);
    } catch (error) {
      console.error("Error signing in:", error);
    }
  };

  return <button onClick={handleSignIn}>Sign In with Google</button>;
}

export default SignInWithPopup;
```

---

## 9. Signing In with Redirect (`signInWithRedirect`)

**Function Structure**:

```javascript
signInWithRedirect(auth, provider);
```

**Description**:

- Redirects the user to the provider's sign-in page.
- Use in environments where popups are not allowed.

**React Example**:

```jsx
import { auth, googleProvider } from "./firebase";
import { signInWithRedirect } from "firebase/auth";

function SignInWithRedirect() {
  const handleSignIn = () => {
    signInWithRedirect(auth, googleProvider);
  };

  return <button onClick={handleSignIn}>Sign In with Google</button>;
}

export default SignInWithRedirect;
```

---

## 10. Getting Redirect Results (`getRedirectResult`)

**Function Structure**:

```javascript
getRedirectResult(auth);
```

**Description**:

- Retrieves the result of a redirect sign-in operation.
- Use this function after the user is redirected back to your app.

**React Example**:

```jsx
import { useEffect } from "react";
import { auth } from "./firebase";
import { getRedirectResult } from "firebase/auth";

function HandleRedirectResult() {
  useEffect(() => {
    const checkRedirect = async () => {
      try {
        const result = await getRedirectResult(auth);
        if (result) {
          console.log("Redirect User Info:", result.user);
        }
      } catch (error) {
        console.error("Error getting redirect result:", error);
      }
    };

    checkRedirect();
  }, []);

  return <div>Handling Redirect...</div>;
}

export default HandleRedirectResult;
```

---

## 11. Verifying Password Reset Code (`verifyPasswordResetCode`)

**Function Structure**:

```javascript
verifyPasswordResetCode(auth, code);
```

**Description**:

- Verifies the password reset code sent to the user's email.
- Returns a `Promise` that resolves with the user's email address if valid.

**React Example**:

```jsx
import { useState } from "react";
import { auth } from "./firebase";
import { verifyPasswordResetCode } from "firebase/auth";

function VerifyResetCode({ code }) {
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");

  const handleVerify = async () => {
    try {
      const emailAddress = await verifyPasswordResetCode(auth, code);
      setEmail(emailAddress);
      setMessage("Code verified successfully!");
    } catch (error) {
      setMessage("Error: " + error.message);
    }
  };

  return (
    <div>
      <button onClick={handleVerify}>Verify Code</button>
      <p>{message}</p>
      {email && <p>Email: {email}</p>}
    </div>
  );
}

export default VerifyResetCode;
```

---

## 12. Confirming Password Reset (`confirmPasswordReset`)

**Function Structure**:

```javascript
confirmPasswordReset(auth, code, newPassword);
```

**Description**:

- Confirms the password reset using a verification code and new password.
- Returns a `Promise` that resolves when the password is reset successfully.

**React Example**:

```jsx
import { useState } from "react";
import { auth } from "./firebase";
import { confirmPasswordReset } from "firebase/auth";

function ConfirmResetPassword({ code }) {
  const [newPassword, setNewPassword] = useState("");
  const [message, setMessage] = useState("");

  const handleReset = async () => {
    try {
      await confirmPasswordReset(auth, code, newPassword);
      setMessage("Password reset successfully!");
    } catch (error) {
      setMessage("Error: " + error.message);
    }
  };

  return (
    <div>
      <input
        type="password"
        placeholder="New Password"
        value={newPassword}
        onChange={(e) => setNewPassword(e.target.value)}
      />
      <button onClick={handleReset}>Reset Password</button>
      <p>{message}</p>
    </div>
  );
}

export default ConfirmResetPassword;
```

---

## 13. Linking with Credential (`linkWithCredential`)

**Function Structure**:

```javascript
linkWithCredential(user, credential);
```

**Description**:

- Links a third-party provider or another credential to the currently signed-in user.
- Useful for associating multiple sign-in methods with the same user account.

**React Example**:

```jsx
import { auth, googleProvider } from "./firebase";
import {
  GoogleAuthProvider,
  linkWithCredential,
  signInWithPopup,
} from "firebase/auth";

function LinkWithGoogle() {
  const handleLink = async () => {
    try {
      const result = await signInWithPopup(auth, googleProvider);
      const credential = GoogleAuthProvider.credentialFromResult(result);
      if (auth.currentUser && credential) {
        await linkWithCredential(auth.currentUser, credential);
        console.log("Account linked successfully!");
      }
    } catch (error) {
      console.error("Error linking account:", error);
    }
  };

  return <button onClick={handleLink}>Link with Google</button>;
}

export default LinkWithGoogle;
```

---

## 14. Reauthenticating with Credential (`reauthenticateWithCredential`)

**Function Structure**:

```javascript
reauthenticateWithCredential(user, credential);
```

**Description**:

- Reauthenticates a user with a credential, often required for sensitive operations.

**React Example**:

```jsx
import { auth } from "./firebase";
import { EmailAuthProvider, reauthenticateWithCredential } from "firebase/auth";

function ReauthenticateUser() {
  const handleReauthenticate = async () => {
    const email = "user@example.com";
    const password = "userPassword";

    try {
      const credential = EmailAuthProvider.credential(email, password);
      if (auth.currentUser) {
        await reauthenticateWithCredential(auth.currentUser, credential);
        console.log("User reauthenticated successfully!");
      }
    } catch (error) {
      console.error("Error reauthenticating user:", error);
    }
  };

  return <button onClick={handleReauthenticate}>Reauthenticate</button>;
}

export default ReauthenticateUser;
```

## 15. Function to Update Password (`updatePassword`)

**Function Structure**:

```javascript
updatePassword(user, newPassword);
```

**Description**:

- Updates the password for the currently signed-in user.
- Requires the `user` object, which represents the authenticated user.
- Returns a Promise that resolves when the password update is successful.

**Steps**:

1. Get the `user` object of the currently signed-in user using `auth.currentUser`.
2. Call the `updatePassword` method with the `user` object and the new password.
3. Handle success or error responses appropriately.

---

## React Example

**Code Example**:

```jsx
import { useState } from "react";
import { getAuth, updatePassword } from "firebase/auth";

function UpdatePassword() {
  const [newPassword, setNewPassword] = useState("");
  const [message, setMessage] = useState("");

  const handlePasswordUpdate = async (event) => {
    event.preventDefault();
    const auth = getAuth();
    const user = auth.currentUser;

    if (user) {
      try {
        await updatePassword(user, newPassword);
        setMessage("Password updated successfully!");
      } catch (error) {
        setMessage("Error updating password: " + error.message);
      }
    } else {
      setMessage("No user is currently signed in.");
    }
  };

  return (
    <div>
      <h2>Update Password</h2>
      <form onSubmit={handlePasswordUpdate}>
        <div>
          <label htmlFor="newPassword">New Password:</label>
          <input
            type="password"
            id="newPassword"
            value={newPassword}
            onChange={(e) => setNewPassword(e.target.value)}
          />
        </div>
        <button type="submit">Update Password</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default UpdatePassword;
```

---

## Notes

- **Reauthentication Required**:
  If the user hasn't recently signed in, Firebase may throw an error indicating that the user's credentials are too old. In such cases, you need to reauthenticate the user before updating the password.

- **Password Validation**:
  Ensure that the new password meets the security requirements (e.g., minimum length, special characters, etc.) for your application.

---

## Reauthentication Example

If you encounter the "requires-recent-login" error, use the `reauthenticateWithCredential` method to reauthenticate the user before updating the password.

```javascript
import {
  EmailAuthProvider,
  reauthenticateWithCredential,
  updatePassword,
} from "firebase/auth";

async function reauthenticateAndChangePassword(
  user,
  email,
  currentPassword,
  newPassword
) {
  const credential = EmailAuthProvider.credential(email, currentPassword);
  try {
    await reauthenticateWithCredential(user, credential);
    await updatePassword(user, newPassword);
    console.log("Password updated successfully after reauthentication.");
  } catch (error) {
    console.error("Error: ", error.message);
  }
}
```

---

This covers the steps and examples to update a user's password using Firebase Authentication. Let me know if you need further clarification or additional examples!
