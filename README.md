# firebase File Upload

### [YouTube Link](https://www.youtube.com/watch?v=YOAeBSCkArA)

Built on **React** using `create-react-app`

## Getting Started
### Follow these steps while starting the project

### 1.  Create react app

    npx create-react-app .

### 2.  FireBase - database rules

    rules_version = '2';
    service cloud.firestore {
    match /databases/{database}/documents {
        match /{document=**} {
        allow read, write: if true;
        }
    }
    }

### 3.1  Setting up

#### 1.  Dependancies

    import { useState, useEffect } from "react";
    import {
    ref,
    uploadBytes,
    getDownloadURL,
    listAll,
    list,
    } from "firebase/storage";
    import { storage } from "./firebase";
    import { v4 } from "uuid";

#### 2.  Upload URL

    const [imageUpload, setImageUpload] = useState(null);
    const [imageUrls, setImageUrls] = useState([]);

#### 3.  Database Connection

    const imagesListRef = ref(storage, "images/");
    const uploadFile = () => {
        if (imageUpload == null) return;
        const imageRef = ref(storage, `images/${imageUpload.name + v4()}`);
        uploadBytes(imageRef, imageUpload).then((snapshot) => {
        getDownloadURL(snapshot.ref).then((url) => {
            setImageUrls((prev) => [...prev, url]);
        });
        });
    };

#### 4.  `useEffect` For dynamic changes

    useEffect(() => {
        listAll(imagesListRef).then((response) => {
        response.items.forEach((item) => {
            getDownloadURL(item).then((url) => {
            setImageUrls((prev) => [...prev, url]);
            });
        });
        });
    }, []);


#### 5.  Final output presentation

    return (
        <div className="App">
        <input
            type="file"
            onChange={(event) => {
            setImageUpload(event.target.files[0]);
            }}
        />
        <button onClick={uploadFile}> Upload Image</button>
        {imageUrls.map((url) => {
            return <img src={url} />;
        })}
        </div>
    );
    }

