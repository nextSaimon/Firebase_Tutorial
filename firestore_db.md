# Firestore Database Functions with React Examples

## 1. Data Creation (`addDoc`)

**Function Structure**:

```javascript
addDoc(collection(db, "collectionName"), data);
```

**Description**:

- Adds a new document to a specified collection
- Generates a unique document ID automatically
- Returns a reference to the newly created document

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { collection, addDoc } from "firebase/firestore";

function CreateUser() {
  const [userData, setUserData] = useState({ name: "", email: "" });
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      const docRef = await addDoc(collection(db, "users"), userData);
      setMessage(`User created with ID: ${docRef.id}`);
    } catch (e) {
      setMessage("Error adding document: " + e);
    }
  };

  const handleChange = (event) => {
    setUserData({ ...userData, [event.target.name]: event.target.value });
  };

  return (
    <div>
      <h2>Create User</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="name">Name:</label>
          <input
            type="text"
            id="name"
            name="name"
            value={userData.name}
            onChange={handleChange}
          />
        </div>
        <div>
          <label htmlFor="email">Email:</label>
          <input
            type="email"
            id="email"
            name="email"
            value={userData.email}
            onChange={handleChange}
          />
        </div>
        <button type="submit">Create</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default CreateUser;
```

---

## 2. Data Creation with a Custom ID (`setDoc`)

**Function Structure**:

```javascript
setDoc(doc(collection(db, "collectionName"), customId), data);
```

**Description**:

- Allows you to set a custom document ID in Firestore
- Overwrites the document if the custom ID already exists
- Returns a `Promise` that resolves when the operation completes

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { collection, doc, setDoc } from "firebase/firestore";

function CreateUserWithCustomId() {
  const [userData, setUserData] = useState({ name: "", email: "" });
  const [customId, setCustomId] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      await setDoc(doc(collection(db, "users"), customId), userData);
      setMessage(`User created with custom ID: ${customId}`);
    } catch (e) {
      setMessage("Error adding document: " + e);
    }
  };

  const handleChange = (event) => {
    setUserData({ ...userData, [event.target.name]: event.target.value });
  };

  const handleIdChange = (event) => {
    setCustomId(event.target.value);
  };

  return (
    <div>
      <h2>Create User with Custom ID</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="customId">Custom ID:</label>
          <input
            type="text"
            id="customId"
            name="customId"
            value={customId}
            onChange={handleIdChange}
          />
        </div>
        <div>
          <label htmlFor="name">Name:</label>
          <input
            type="text"
            id="name"
            name="name"
            value={userData.name}
            onChange={handleChange}
          />
        </div>
        <div>
          <label htmlFor="email">Email:</label>
          <input
            type="email"
            id="email"
            name="email"
            value={userData.email}
            onChange={handleChange}
          />
        </div>
        <button type="submit">Create</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default CreateUserWithCustomId;
```

**Steps**:

1. Import `doc` and `setDoc` from Firebase Firestore.
2. Create a `customId` state to hold the user-defined ID.
3. Use the `doc` function with the collection reference and `customId` to specify the document.
4. Pass the data object to the `setDoc` function.
5. Handle success or error messages appropriately.

---

**Key Differences Between `addDoc` and `setDoc`**:

| Feature                   | `addDoc`                | `setDoc`                                 |
| ------------------------- | ----------------------- | ---------------------------------------- |
| Document ID               | Automatically generated | Customizable (user-defined)              |
| Overwriting Existing Data | No                      | Yes (if custom ID exists, it overwrites) |
| Ease of Use               | Simple and quick        | Requires additional ID management        |

---

## 3. Data Retrieval (`getDocs`)

**Function Structure**:

```javascript
getDocs(collection(db, "collectionName"));
// Or with query
getDocs(query(collection(db, "collectionName"), ...queryConditions));
```

**Description**:

- Retrieves multiple documents from a collection
- Can be used with or without query conditions
- Returns a snapshot containing all matched documents

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { collection, getDocs, query, where } from "firebase/firestore";

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        // Basic retrieval
        // const querySnapshot = await getDocs(collection(db, 'users'));

        // Filtered retrieval
        const q = query(
          collection(db, "users"),
          where("status", "==", "active")
        );
        const querySnapshot = await getDocs(q);

        const userList = querySnapshot.docs.map((doc) => ({
          id: doc.id,
          ...doc.data(),
        }));

        setUsers(userList);
        setLoading(false);
      } catch (error) {
        console.error("Error fetching users:", error);
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h2>Active Users</h2>
      {users.map((user) => (
        <div key={user.id}>
          <p>Name: {user.name}</p>
          <p>Email: {user.email}</p>
        </div>
      ))}
    </div>
  );
}

export default UserList;
```

## 4. Single Document Retrieval (`getDoc`)

**Function Structure**:

```javascript
getDoc(doc(db, "collectionName", "documentId"));
```

**Description**:

- Retrieves a single document by its specific document ID
- Useful for fetching precise, known document references
- Returns a document snapshot with data and metadata

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { doc, getDoc } from "firebase/firestore";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUserProfile = async () => {
      try {
        const docRef = doc(db, "users", userId);
        const docSnap = await getDoc(docRef);

        if (docSnap.exists()) {
          setUser({ id: docSnap.id, ...docSnap.data() });
        } else {
          console.log("No such document!");
        }
        setLoading(false);
      } catch (error) {
        console.error("Error fetching user:", error);
        setLoading(false);
      }
    };

    fetchUserProfile();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h2>User Profile</h2>
      <p>Name: {user.name}</p>
      <p>Email: {user.email}</p>
    </div>
  );
}

export default UserProfile;
```

## 5. Data Update (`updateDoc`)

**Function Structure**:

```javascript
updateDoc(doc(db, "collectionName", "documentId"), { fieldToUpdate: newValue });
```

**Description**:

- Modifies specific fields in an existing document
- Allows partial updates without replacing entire document
- Provides atomic updates to prevent data loss

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { doc, updateDoc } from "firebase/firestore";

function UpdateUserProfile({ userId }) {
  const [userData, setUserData] = useState({
    name: "",
    email: "",
  });
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      const userRef = doc(db, "users", userId);
      await updateDoc(userRef, userData);
      setMessage("Profile updated successfully");
    } catch (error) {
      setMessage("Error updating profile: " + error);
    }
  };

  const handleChange = (event) => {
    setUserData({
      ...userData,
      [event.target.name]: event.target.value,
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="name"
        value={userData.name}
        onChange={handleChange}
        placeholder="Name"
      />
      <input
        type="email"
        name="email"
        value={userData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <button type="submit">Update Profile</button>
      {message && <p>{message}</p>}
    </form>
  );
}

export default UpdateUserProfile;
```

## 6. Data Deletion (`deleteDoc`)

**Function Structure**:

```javascript
deleteDoc(doc(db, "collectionName", "documentId"));
```

**Description**:

- Completely removes a document from a collection
- Permanent operation, cannot be undone
- Requires specific document reference

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { doc, deleteDoc } from "firebase/firestore";

function DeleteUser({ userId }) {
  const [message, setMessage] = useState("");

  const handleDelete = async () => {
    try {
      const userRef = doc(db, "users", userId);
      await deleteDoc(userRef);
      setMessage("User deleted successfully");
    } catch (error) {
      setMessage("Error deleting user: " + error);
    }
  };

  return (
    <div>
      <button onClick={handleDelete}>Delete User</button>
      {message && <p>{message}</p>}
    </div>
  );
}

export default DeleteUser;
```

### Note:

All examples assume:

- Firebase configuration is set up
- Proper imports are in place
- Error handling is implemented
- State management is considered

Would you like me to elaborate on any specific aspect of these Firestore operations?

## 7. Real-time Streaming (`onSnapshot`)

**Function Structure**:

```javascript
onSnapshot(collection(db, 'collectionName'), (snapshot) => { ... })
```

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { collection, onSnapshot } from "firebase/firestore";

function RealtimeUserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const unsubscribe = onSnapshot(
      collection(db, "users"),
      (snapshot) => {
        const userList = snapshot.docs.map((doc) => ({
          id: doc.id,
          ...doc.data(),
        }));
        setUsers(userList);
      },
      (error) => {
        console.error("Snapshot error:", error);
      }
    );

    // Cleanup subscription on component unmount
    return () => unsubscribe();
  }, []);

  return (
    <div>
      {users.map((user) => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}

export default RealtimeUserList;
```

## 8. Batch Writes

**Function Structure**:

```javascript
const batch = writeBatch(db);
batch.set(docRef, data);
batch.update(docRef, data);
batch.delete(docRef);
await batch.commit();
```

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { writeBatch, doc, collection } from "firebase/firestore";

function BatchOperation() {
  const [message, setMessage] = useState("");

  const performBatchWrite = async () => {
    try {
      const batch = writeBatch(db);

      const user1Ref = doc(collection(db, "users"), "user1");
      const user2Ref = doc(collection(db, "users"), "user2");

      batch.set(user1Ref, { name: "John", status: "active" });
      batch.update(user2Ref, { lastLogin: new Date() });

      await batch.commit();
      setMessage("Batch write successful");
    } catch (error) {
      setMessage("Batch write failed: " + error);
    }
  };

  return <button onClick={performBatchWrite}>Perform Batch Write</button>;
}

export default BatchOperation;
```

## 9. Compound Queries

**Function Structure**:

```javascript
query(
  collection(db, "collectionName"),
  where("field1", "operator", value1),
  where("field2", "operator", value2),
  orderBy("sortField")
);
```

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { collection, query, where, orderBy, getDocs } from "firebase/firestore";

function AdvancedUserSearch() {
  const [filteredUsers, setFilteredUsers] = useState([]);

  useEffect(() => {
    const fetchFilteredUsers = async () => {
      const q = query(
        collection(db, "users"),
        where("age", ">=", 18),
        where("status", "==", "active"),
        orderBy("name")
      );

      const querySnapshot = await getDocs(q);
      const results = querySnapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
      }));

      setFilteredUsers(results);
    };

    fetchFilteredUsers();
  }, []);

  return (
    <div>
      {filteredUsers.map((user) => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}

export default AdvancedUserSearch;
```

## 10. Offline Persistence

**Function Structure**:

```javascript
enableIndexedDbPersistence(db);
```

**React Example**:

```jsx
import { useEffect } from "react";
import { db } from "./firebase";
import { enableIndexedDbPersistence } from "firebase/firestore";

function OfflineSupport() {
  useEffect(() => {
    const enableOffline = async () => {
      try {
        await enableIndexedDbPersistence(db);
        console.log("Offline persistence enabled");
      } catch (error) {
        console.error("Offline persistence failed:", error);
      }
    };

    enableOffline();
  }, []);

  return null;
}

export default OfflineSupport;
```

## 11. Collection Delete

**Function Structure**:

```javascript
// Custom function to delete entire collection
const deleteCollection = async (collectionPath) => { ... }
```

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { collection, query, getDocs, doc, deleteDoc } from "firebase/firestore";

function DeleteCollection() {
  const [message, setMessage] = useState("");

  const deleteEntireCollection = async (collectionPath) => {
    const q = query(collection(db, collectionPath));
    const querySnapshot = await getDocs(q);

    const deletePromises = querySnapshot.docs.map(async (document) => {
      await deleteDoc(doc(db, collectionPath, document.id));
    });

    await Promise.all(deletePromises);
    setMessage(`Deleted all documents in ${collectionPath}`);
  };

  const handleDeleteUsers = () => {
    deleteEntireCollection("users");
  };

  return <button onClick={handleDeleteUsers}>Delete All Users</button>;
}

export default DeleteCollection;
```

## 12. Geospatial Queries

**Function Structure**:

```javascript
query(collection(db, "locations"), where("location", "near", geopoint));
```

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { collection, query, where, getDocs } from "firebase/firestore";
import { GeoPoint } from "firebase/firestore";

function NearbyLocations() {
  const [locations, setLocations] = useState([]);

  useEffect(() => {
    const findNearbyPlaces = async () => {
      const centerPoint = new GeoPoint(40.7128, -74.006);
      const q = query(
        collection(db, "locations"),
        where("location", "<=", centerPoint)
      );

      const querySnapshot = await getDocs(q);
      const nearbyPlaces = querySnapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
      }));

      setLocations(nearbyPlaces);
    };

    findNearbyPlaces();
  }, []);

  return (
    <div>
      {locations.map((location) => (
        <div key={location.id}>{location.name}</div>
      ))}
    </div>
  );
}

export default NearbyLocations;
```

## 13. Transactions

**Function Structure**:

```javascript
runTransaction(db, async (transaction) => {
  const docRef = doc(db, "collection", "documentId");
  const docSnap = await transaction.get(docRef);
  transaction.update(docRef, updatedData);
});
```

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { doc, runTransaction } from "firebase/firestore";

function TransferBalance() {
  const [message, setMessage] = useState("");

  const transferFunds = async (fromUserId, toUserId, amount) => {
    const fromRef = doc(db, "accounts", fromUserId);
    const toRef = doc(db, "accounts", toUserId);

    try {
      await runTransaction(db, async (transaction) => {
        const fromDoc = await transaction.get(fromRef);
        const toDoc = await transaction.get(toRef);

        if (!fromDoc.exists() || !toDoc.exists()) {
          throw new Error("Document does not exist");
        }

        const fromBalance = fromDoc.data().balance;
        const toBalance = toDoc.data().balance;

        if (fromBalance < amount) {
          throw new Error("Insufficient funds");
        }

        transaction.update(fromRef, {
          balance: fromBalance - amount,
        });
        transaction.update(toRef, {
          balance: toBalance + amount,
        });
      });

      setMessage("Transfer successful");
    } catch (error) {
      setMessage(`Transfer failed: ${error.message}`);
    }
  };

  return (
    <button onClick={() => transferFunds("user1", "user2", 100)}>
      Transfer Funds
    </button>
  );
}

export default TransferBalance;
```

## 14. Security Rules (Client-Side Example)

**Function Structure**:

```javascript
// Firestore rules implemented in Firebase Console
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }
  }
}
```

**React Authentication Example**:

```jsx
import { useState, useEffect } from "react";
import { db, auth } from "./firebase";
import { doc, getDoc } from "firebase/firestore";

function SecureUserProfile() {
  const [userProfile, setUserProfile] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUserProfile = async () => {
      const user = auth.currentUser;
      if (!user) {
        setError("Not authenticated");
        return;
      }

      try {
        const docRef = doc(db, "users", user.uid);
        const docSnap = await getDoc(docRef);

        if (docSnap.exists()) {
          setUserProfile(docSnap.data());
        } else {
          setError("No profile found");
        }
      } catch (error) {
        setError("Access denied");
      }
    };

    fetchUserProfile();
  }, []);

  if (error) return <div>{error}</div>;
  if (!userProfile) return <div>Loading...</div>;

  return (
    <div>
      <h2>User Profile</h2>
      <p>Name: {userProfile.name}</p>
    </div>
  );
}

export default SecureUserProfile;
```

## 15. Collection Group Queries

**Function Structure**:

```javascript
query(collectionGroup("subcollectionName"), where("field", "operator", value));
```

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { collectionGroup, query, where, getDocs } from "firebase/firestore";

function GlobalComments() {
  const [comments, setComments] = useState([]);

  useEffect(() => {
    const fetchGlobalComments = async () => {
      const q = query(collectionGroup(db, "comments"), where("likes", ">", 10));

      const querySnapshot = await getDocs(q);
      const globalComments = querySnapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
        path: doc.ref.path,
      }));

      setComments(globalComments);
    };

    fetchGlobalComments();
  }, []);

  return (
    <div>
      {comments.map((comment) => (
        <div key={comment.id}>
          <p>{comment.text}</p>
          <p>Likes: {comment.likes}</p>
        </div>
      ))}
    </div>
  );
}

export default GlobalComments;
```

## 16. Custom Firestore Hooks

**Custom Hook Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { collection, query, where, onSnapshot } from "firebase/firestore";

function useFirestoreCollection(collectionName, queryConstraints = []) {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const collectionRef = collection(db, collectionName);
    const q = queryConstraints.length
      ? query(collectionRef, ...queryConstraints)
      : collectionRef;

    const unsubscribe = onSnapshot(
      q,
      (snapshot) => {
        const results = snapshot.docs.map((doc) => ({
          id: doc.id,
          ...doc.data(),
        }));
        setData(results);
        setLoading(false);
      },
      (error) => {
        setError(error);
        setLoading(false);
      }
    );

    return () => unsubscribe();
  }, [collectionName, JSON.stringify(queryConstraints)]);

  return { data, loading, error };
}

// Usage Example
function UserList() {
  const {
    data: users,
    loading,
    error,
  } = useFirestoreCollection("users", [where("status", "==", "active")]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      {users.map((user) => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}
```
