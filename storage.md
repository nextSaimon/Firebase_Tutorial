# Firebase Storage Functions with React Examples

## 1. Firebase Storage Setup

### Import and Initialize Firebase Storage

```javascript
import { initializeApp } from "firebase/app";
import { getStorage } from "firebase/storage";

const firebaseConfig = {
  apiKey: "<YOUR_API_KEY>",
  authDomain: "<YOUR_AUTH_DOMAIN>",
  projectId: "<YOUR_PROJECT_ID>",
  storageBucket: "<YOUR_STORAGE_BUCKET>",
  messagingSenderId: "<YOUR_MESSAGING_SENDER_ID>",
  appId: "<YOUR_APP_ID>",
};

const app = initializeApp(firebaseConfig);
const storage = getStorage(app);
```

---

## 2. Uploading Files

### Using `storageRef.put()`

**Description**:

- Uploads a file to Firebase Storage using a file reference.

**Example**:

```javascript
import { ref, uploadBytes } from "firebase/storage";
import { useState } from "react";

function FileUploader({ storage }) {
  const [file, setFile] = useState(null);
  const [message, setMessage] = useState("");

  const handleFileChange = (event) => {
    setFile(event.target.files[0]);
  };

  const handleUpload = async () => {
    if (!file) return;
    const storageRef = ref(storage, `uploads/${file.name}`);
    try {
      await uploadBytes(storageRef, file);
      setMessage("File uploaded successfully!");
    } catch (error) {
      setMessage("Error uploading file: " + error);
    }
  };

  return (
    <div>
      <h2>Upload File</h2>
      <input type="file" onChange={handleFileChange} />
      <button onClick={handleUpload}>Upload</button>
      <p>{message}</p>
    </div>
  );
}

export default FileUploader;
```

### Using `storageRef.putString()`

**Description**:

- Uploads raw data as a string to Firebase Storage.

**Example**:

```javascript
import { ref, uploadString } from "firebase/storage";
import { useState } from "react";

function Base64Uploader({ storage }) {
  const [base64String, setBase64String] = useState("");
  const [message, setMessage] = useState("");

  const handleUpload = async () => {
    if (!base64String) return;
    const storageRef = ref(storage, "images/base64image.jpg");
    try {
      await uploadString(storageRef, base64String, "data_url");
      setMessage("Image uploaded successfully!");
    } catch (error) {
      setMessage("Error uploading image: " + error);
    }
  };

  return (
    <div>
      <h2>Upload Base64 String</h2>
      <textarea
        placeholder="Paste base64 string here"
        value={base64String}
        onChange={(e) => setBase64String(e.target.value)}
      ></textarea>
      <button onClick={handleUpload}>Upload</button>
      <p>{message}</p>
    </div>
  );
}

export default Base64Uploader;
```

---

## 3. Downloading Files

### Using `storageRef.getDownloadURL()`

**Description**:

- Retrieves the download URL of a file stored in Firebase Storage.

**Example**:

```javascript
import { ref, getDownloadURL } from "firebase/storage";
import { useState } from "react";

function FileDownloader({ storage }) {
  const [filePath, setFilePath] = useState("");
  const [fileURL, setFileURL] = useState("");

  const handleDownload = async () => {
    if (!filePath) return;
    const storageRef = ref(storage, filePath);
    try {
      const url = await getDownloadURL(storageRef);
      setFileURL(url);
    } catch (error) {
      setFileURL("Error fetching file: " + error);
    }
  };

  return (
    <div>
      <h2>Download File</h2>
      <input
        type="text"
        placeholder="Enter file path"
        value={filePath}
        onChange={(e) => setFilePath(e.target.value)}
      />
      <button onClick={handleDownload}>Get URL</button>
      {fileURL && (
        <p>
          File URL:{" "}
          <a href={fileURL} target="_blank" rel="noopener noreferrer">
            {fileURL}
          </a>
        </p>
      )}
    </div>
  );
}

export default FileDownloader;
```

---

## 4. Managing Files

### Listing All Files (`storageRef.listAll()`)

**Example**:

```javascript
import { ref, listAll } from "firebase/storage";
import { useState } from "react";

function FileLister({ storage }) {
  const [folderPath, setFolderPath] = useState("");
  const [files, setFiles] = useState([]);

  const handleListFiles = async () => {
    if (!folderPath) return;
    const folderRef = ref(storage, folderPath);
    try {
      const result = await listAll(folderRef);
      setFiles(result.items.map((item) => item.name));
    } catch (error) {
      console.error("Error listing files:", error);
    }
  };

  return (
    <div>
      <h2>List Files</h2>
      <input
        type="text"
        placeholder="Enter folder path"
        value={folderPath}
        onChange={(e) => setFolderPath(e.target.value)}
      />
      <button onClick={handleListFiles}>List Files</button>
      <ul>
        {files.map((file, index) => (
          <li key={index}>{file}</li>
        ))}
      </ul>
    </div>
  );
}

export default FileLister;
```

### Deleting Files (`storageRef.delete()`)

**Example**:

```javascript
import { ref, deleteObject } from "firebase/storage";
import { useState } from "react";

function FileDeleter({ storage }) {
  const [filePath, setFilePath] = useState("");
  const [message, setMessage] = useState("");

  const handleDelete = async () => {
    if (!filePath) return;
    const fileRef = ref(storage, filePath);
    try {
      await deleteObject(fileRef);
      setMessage("File deleted successfully!");
    } catch (error) {
      setMessage("Error deleting file: " + error);
    }
  };

  return (
    <div>
      <h2>Delete File</h2>
      <input
        type="text"
        placeholder="Enter file path"
        value={filePath}
        onChange={(e) => setFilePath(e.target.value)}
      />
      <button onClick={handleDelete}>Delete</button>
      <p>{message}</p>
    </div>
  );
}

export default FileDeleter;
```

---

## 5. Handling File Metadata

### Updating Metadata (`fileRef.updateMetadata()`)

**Example**:

```javascript
import { ref, updateMetadata } from "firebase/storage";
import { useState } from "react";

function MetadataUpdater({ storage }) {
  const [filePath, setFilePath] = useState("");
  const [metadata, setMetadata] = useState({});
  const [message, setMessage] = useState("");

  const handleUpdateMetadata = async () => {
    if (!filePath || !metadata) return;
    const fileRef = ref(storage, filePath);
    try {
      await updateMetadata(fileRef, metadata);
      setMessage("Metadata updated successfully!");
    } catch (error) {
      setMessage("Error updating metadata: " + error);
    }
  };

  return (
    <div>
      <h2>Update Metadata</h2>
      <input
        type="text"
        placeholder="Enter file path"
        value={filePath}
        onChange={(e) => setFilePath(e.target.value)}
      />
      <textarea
        placeholder="Enter metadata as JSON"
        value={JSON.stringify(metadata)}
        onChange={(e) => setMetadata(JSON.parse(e.target.value))}
      ></textarea>
      <button onClick={handleUpdateMetadata}>Update Metadata</button>
      <p>{message}</p>
    </div>
  );
}

export default MetadataUpdater;
```

### Retrieving Metadata (`fileRef.getMetadata()`)

**Example**:

```javascript
import { ref, getMetadata } from "firebase/storage";
import { useState } from "react";

function MetadataRetriever({ storage }) {
  const [filePath, setFilePath] = useState("");
  const [metadata, setMetadata] = useState(null);

  const handleGetMetadata = async () => {
    if (!filePath) return;
    const fileRef = ref(storage, filePath);
    try {
      const metadata = await getMetadata(fileRef);
      setMetadata(metadata);
    } catch (error) {
      console.error("Error retrieving metadata:", error);
    }
  };

  return (
    <div>
      <h2>Retrieve Metadata</h2>
      <input
        type="text"
        placeholder="Enter file path"
        value={filePath}
        onChange={(e) => setFilePath(e.target.value)}
      />
      <button onClick={handleGetMetadata}>Get Metadata</button>
      {metadata && <pre>{JSON.stringify(metadata, null, 2)}</pre>}
    </div>
  );
}

export default MetadataRetriever;
```

---

## 6. Firebase Storage Security Rules

### Custom Rules in Firebase Console

- Navigate to the **Firebase Console** > **Storage** > **Rules**.
- Write custom rules to manage access and permissions.

**Example Rule**:

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## 7. File Upload Progress

### Tracking Progress with `uploadTask.on('state_changed')`

**Example**:

```javascript
import { ref, uploadBytesResumable } from "firebase/storage";
import { useState } from "react";

function FileUploaderWithProgress({ storage }) {
  const [file, setFile] = useState(null);
  const [progress, setProgress] = useState(0);
  const [message, setMessage] = useState("");

  const handleFileChange = (event) => {
    setFile(event.target.files[0]);
  };

  const handleUpload = () => {
    if (!file) return;
    const storageRef = ref(storage, `uploads/${file.name}`);
    const uploadTask = uploadBytesResumable(storageRef, file);

    uploadTask.on(
      "state_changed",
      (snapshot) => {
        const progress =
          (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
        setProgress(progress);
      },
      (error) => {
        setMessage("Error during upload: " + error);
      },
      () => {
        setMessage("Upload completed successfully!");
      }
    );
  };

  return (
    <div>
      <h2>Upload File with Progress</h2>
      <input type="file" onChange={handleFileChange} />
      <button onClick={handleUpload}>Upload</button>
      <p>Progress: {progress}%</p>
      <p>{message}</p>
    </div>
  );
}

export default FileUploaderWithProgress;
```
