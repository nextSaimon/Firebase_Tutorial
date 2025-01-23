# Realtime Database Functions with React Examples

## 1. Data Creation (`set`)

**Function Structure**:

```javascript
set(ref(db, 'path/to/data'), data);
```

**Description**:

- Writes or updates data to the specified path in the Realtime Database.
- Overwrites the existing data if the path already exists.
- Returns a `Promise` that resolves when the operation completes.

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { ref, set } from "firebase/database";

function CreateUser() {
  const [userData, setUserData] = useState({ name: "", email: "" });
  const [userId, setUserId] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      await set(ref(db, `users/${userId}`), userData);
      setMessage("User created successfully!");
    } catch (e) {
      setMessage("Error adding user: " + e);
    }
  };

  const handleChange = (event) => {
    setUserData({ ...userData, [event.target.name]: event.target.value });
  };

  const handleIdChange = (event) => {
    setUserId(event.target.value);
  };

  return (
    <div>
      <h2>Create User</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="userId">User ID:</label>
          <input
            type="text"
            id="userId"
            value={userId}
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

export default CreateUser;
```

---

## 2. Data Retrieval (`get`)

**Function Structure**:

```javascript
get(ref(db, 'path/to/data'));
```

**Description**:

- Reads data from the specified path in the Realtime Database.
- Returns a snapshot of the data.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, get } from "firebase/database";

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const snapshot = await get(ref(db, "users"));
        if (snapshot.exists()) {
          setUsers(Object.entries(snapshot.val()));
        } else {
          console.log("No data available");
        }
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
      <h2>User List</h2>
      {users.map(([id, user]) => (
        <div key={id}>
          <p>ID: {id}</p>
          <p>Name: {user.name}</p>
          <p>Email: {user.email}</p>
        </div>
      ))}
    </div>
  );
}

export default UserList;
```

---

## 3. Data Update (`update`)

**Function Structure**:

```javascript
update(ref(db, 'path/to/data'), updatedData);
```

**Description**:

- Updates specific fields of a node without overwriting the entire data.
- Returns a `Promise` that resolves when the operation completes.

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { ref, update } from "firebase/database";

function UpdateUser() {
  const [userId, setUserId] = useState("");
  const [updatedData, setUpdatedData] = useState({ name: "", email: "" });
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      await update(ref(db, `users/${userId}`), updatedData);
      setMessage("User updated successfully!");
    } catch (e) {
      setMessage("Error updating user: " + e);
    }
  };

  const handleChange = (event) => {
    setUpdatedData({ ...updatedData, [event.target.name]: event.target.value });
  };

  const handleIdChange = (event) => {
    setUserId(event.target.value);
  };

  return (
    <div>
      <h2>Update User</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="userId">User ID:</label>
          <input
            type="text"
            id="userId"
            value={userId}
            onChange={handleIdChange}
          />
        </div>
        <div>
          <label htmlFor="name">Name:</label>
          <input
            type="text"
            id="name"
            name="name"
            value={updatedData.name}
            onChange={handleChange}
          />
        </div>
        <div>
          <label htmlFor="email">Email:</label>
          <input
            type="email"
            id="email"
            name="email"
            value={updatedData.email}
            onChange={handleChange}
          />
        </div>
        <button type="submit">Update</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default UpdateUser;
```

---

## 4. Data Deletion (`remove`)

**Function Structure**:

```javascript
remove(ref(db, 'path/to/data'));
```

**Description**:

- Deletes data at the specified path in the Realtime Database.
- Returns a `Promise` that resolves when the operation completes.

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { ref, remove } from "firebase/database";

function DeleteUser() {
  const [userId, setUserId] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      await remove(ref(db, `users/${userId}`));
      setMessage("User deleted successfully!");
    } catch (e) {
      setMessage("Error deleting user: " + e);
    }
  };

  const handleIdChange = (event) => {
    setUserId(event.target.value);
  };

  return (
    <div>
      <h2>Delete User</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="userId">User ID:</label>
          <input
            type="text"
            id="userId"
            value={userId}
            onChange={handleIdChange}
          />
        </div>
        <button type="submit">Delete</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default DeleteUser;
```
---

## 5. Data Update (`update`)

**Function Structure**:

```javascript
update(ref(db, "path/to/data"), updates);
```

**Description**:

- Updates specific fields in a database node without overwriting the entire data.
- Takes an object with fields to be updated.
- Returns a `Promise` that resolves when the operation completes.

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { ref, update } from "firebase/database";

function UpdateUser() {
  const [userId, setUserId] = useState("");
  const [userData, setUserData] = useState({});
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      const userRef = ref(db, `users/${userId}`);
      await update(userRef, userData);
      setMessage("User updated successfully!");
    } catch (error) {
      setMessage("Error updating user: " + error);
    }
  };

  const handleChange = (event) => {
    setUserData({ ...userData, [event.target.name]: event.target.value });
  };

  const handleIdChange = (event) => {
    setUserId(event.target.value);
  };

  return (
    <div>
      <h2>Update User</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="userId">User ID:</label>
          <input
            type="text"
            id="userId"
            value={userId}
            onChange={handleIdChange}
          />
        </div>
        <div>
          <label htmlFor="name">Name:</label>
          <input
            type="text"
            id="name"
            name="name"
            onChange={handleChange}
          />
        </div>
        <div>
          <label htmlFor="email">Email:</label>
          <input
            type="email"
            id="email"
            name="email"
            onChange={handleChange}
          />
        </div>
        <button type="submit">Update</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default UpdateUser;
```

---

## 6. Data Deletion (`remove`)

**Function Structure**:

```javascript
remove(ref(db, "path/to/data"));
```

**Description**:

- Deletes data from the specified path in the database.
- Can delete entire nodes or specific fields within a node.
- Returns a `Promise` that resolves when the operation completes.

**React Example**:

```jsx
import { useState } from "react";
import { db } from "./firebase";
import { ref, remove } from "firebase/database";

function DeleteUser() {
  const [userId, setUserId] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      const userRef = ref(db, `users/${userId}`);
      await remove(userRef);
      setMessage("User deleted successfully!");
    } catch (error) {
      setMessage("Error deleting user: " + error);
    }
  };

  const handleIdChange = (event) => {
    setUserId(event.target.value);
  };

  return (
    <div>
      <h2>Delete User</h2>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="userId">User ID:</label>
          <input
            type="text"
            id="userId"
            value={userId}
            onChange={handleIdChange}
          />
        </div>
        <button type="submit">Delete</button>
      </form>
      <p>{message}</p>
    </div>
  );
}

export default DeleteUser;
```

---

## 7. Real-Time Data Listener (`onValue`)

**Function Structure**:

```javascript
onValue(ref(db, "path/to/data"), (snapshot) => {
  const data = snapshot.val();
  // Handle the data
});
```

**Description**:

- Listens to real-time updates at the specified path.
- Automatically triggers when data changes at the specified path.
- Returns a function to unsubscribe from the listener.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, onValue } from "firebase/database";

function RealTimeUserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const usersRef = ref(db, "users");
    const unsubscribe = onValue(usersRef, (snapshot) => {
      const data = snapshot.val();
      const userList = data
        ? Object.keys(data).map((key) => ({ id: key, ...data[key] }))
        : [];
      setUsers(userList);
    });

    // Cleanup listener on unmount
    return () => unsubscribe();
  }, []);

  return (
    <div>
      <h2>Real-Time User List</h2>
      {users.map((user) => (
        <div key={user.id}>
          <p>Name: {user.name}</p>
          <p>Email: {user.email}</p>
        </div>
      ))}
    </div>
  );
}

export default RealTimeUserList;
```

---

## 8. Querying Data (`orderByChild`, `equalTo`, etc.)

**Function Structure**:

```javascript
query(ref(db, "path/to/data"), orderByChild("field"), equalTo(value));
```

**Description**:

- Retrieves filtered data from the database.
- Supports multiple query parameters like `orderByChild`, `limitToFirst`, and `equalTo`.
- Returns a snapshot with the matching data.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, query, orderByChild, equalTo, onValue } from "firebase/database";

function ActiveUsers() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const usersRef = ref(db, "users");
    const activeUsersQuery = query(usersRef, orderByChild("status"), equalTo("active"));

    const unsubscribe = onValue(activeUsersQuery, (snapshot) => {
      const data = snapshot.val();
      const userList = data
        ? Object.keys(data).map((key) => ({ id: key, ...data[key] }))
        : [];
      setUsers(userList);
    });

    return () => unsubscribe();
  }, []);

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

export default ActiveUsers;
```
---
## 9. Pagination and Limiting Data (`limitToFirst`, `limitToLast`)

**Function Structure**:

```javascript
query(ref(db, 'path/to/data'), limitToFirst(n));
query(ref(db, 'path/to/data'), limitToLast(n));
```

**Description**:

- `limitToFirst(n)`: Retrieves the first `n` items in the dataset.
- `limitToLast(n)`: Retrieves the last `n` items in the dataset.
- These are useful for pagination and displaying limited records.
- Used with `onValue` or `get` to fetch the limited results.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, query, limitToFirst, onValue } from "firebase/database";

function PaginatedData() {
  const [data, setData] = useState([]);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const dbRef = ref(db, "items");
        const limitedQuery = query(dbRef, limitToFirst(5));

        onValue(limitedQuery, (snapshot) => {
          if (snapshot.exists()) {
            const result = snapshot.val();
            setData(Object.entries(result).map(([key, value]) => ({ id: key, ...value })));
          } else {
            setData([]);
          }
        });
      } catch (err) {
        setError(err.message);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      <h2>Paginated Data</h2>
      {error && <p>Error: {error}</p>}
      <ul>
        {data.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default PaginatedData;
```

---

## 10. Sorting Data (`orderByValue`, `orderByKey`, `orderByChild`)

**Function Structure**:

```javascript
query(ref(db, 'path/to/data'), orderByChild('childKey'));
query(ref(db, 'path/to/data'), orderByValue());
query(ref(db, 'path/to/data'), orderByKey());
```

**Description**:

- `orderByChild(childKey)`: Orders data by the value of the specified child key.
- `orderByValue()`: Orders data by its value.
- `orderByKey()`: Orders data by its key.
- Use in conjunction with filtering and limiting for complex queries.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, query, orderByChild, onValue } from "firebase/database";

function SortedData() {
  const [data, setData] = useState([]);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const dbRef = ref(db, "users");
        const sortedQuery = query(dbRef, orderByChild("age"));

        onValue(sortedQuery, (snapshot) => {
          if (snapshot.exists()) {
            const result = snapshot.val();
            setData(Object.entries(result).map(([key, value]) => ({ id: key, ...value })));
          } else {
            setData([]);
          }
        });
      } catch (err) {
        setError(err.message);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      <h2>Sorted Data</h2>
      {error && <p>Error: {error}</p>}
      <ul>
        {data.map((item) => (
          <li key={item.id}>{item.name} - Age: {item.age}</li>
        ))}
      </ul>
    </div>
  );
}

export default SortedData;
```

---

## 11. Combining Queries (`startAt`, `endAt`, `equalTo`)

**Function Structure**:

```javascript
query(ref(db, 'path/to/data'), startAt(value));
query(ref(db, 'path/to/data'), endAt(value));
query(ref(db, 'path/to/data'), equalTo(value));
```

**Description**:

- `startAt(value)`: Retrieves items starting from the given value.
- `endAt(value)`: Retrieves items ending at the given value.
- `equalTo(value)`: Retrieves items that match the given value.
- Combine with sorting to refine your queries further.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, query, orderByChild, startAt, endAt, onValue } from "firebase/database";

function FilteredData() {
  const [data, setData] = useState([]);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const dbRef = ref(db, "users");
        const filteredQuery = query(dbRef, orderByChild("age"), startAt(20), endAt(30));

        onValue(filteredQuery, (snapshot) => {
          if (snapshot.exists()) {
            const result = snapshot.val();
            setData(Object.entries(result).map(([key, value]) => ({ id: key, ...value })));
          } else {
            setData([]);
          }
        });
      } catch (err) {
        setError(err.message);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      <h2>Filtered Data</h2>
      {error && <p>Error: {error}</p>}
      <ul>
        {data.map((item) => (
          <li key={item.id}>{item.name} - Age: {item.age}</li>
        ))}
      </ul>
    </div>
  );
}

export default FilteredData;
```

---



## 12. Data Aggregation (`count`, `sum`, `average`)

**Function Structure**:

Firebase Realtime Database does not provide direct aggregation functions like SQL, but you can implement them in the frontend by processing the data after retrieval.

**React Example**:

```jsx
import { useState, useEffect } from "react";
import { db } from "./firebase";
import { ref, onValue } from "firebase/database";

function DataAggregation() {
  const [count, setCount] = useState(0);
  const [sum, setSum] = useState(0);
  const [average, setAverage] = useState(0);

  useEffect(() => {
    const fetchData = async () => {
      const dbRef = ref(db, "items");

      onValue(dbRef, (snapshot) => {
        if (snapshot.exists()) {
          const data = Object.values(snapshot.val());
          const itemCount = data.length;
          const totalSum = data.reduce((acc, item) => acc + (item.value || 0), 0);
          const avg = itemCount > 0 ? totalSum / itemCount : 0;

          setCount(itemCount);
          setSum(totalSum);
          setAverage(avg);
        } else {
          setCount(0);
          setSum(0);
          setAverage(0);
        }
      });
    };

    fetchData();
  }, []);

  return (
    <div>
      <h2>Data Aggregation</h2>
      <p>Count: {count}</p>
      <p>Sum: {sum}</p>
      <p>Average: {average.toFixed(2)}</p>
    </div>
  );
}

export default DataAggregation;
```

---
