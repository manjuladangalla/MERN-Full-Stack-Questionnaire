### MERN Full-Stack Questionnaire

## Part 1: MongoDB

1. Write a MongoDB query to find all documents in a collection where the &quot;age&quot; field is
   greater than 25.

`db.collectionName.find({ age: { $gt: 25 } })`

2. Write a MongoDB query to update the "email" field of a document with a specific ID.

```
db.collectionName.updateOne(
   { _id: ObjectId("documentID") },
   { $set: { email: "newEmail@example.com" } }
)
```

3. Write a MongoDB query to delete all documents in a collection where the "status" field is
   set to "inactive".

`db.collectionName.deleteMany({ status: "inactive" })`

## Part 2: Express

1. Create an Express route that returns a list of all users in a MongoDB collection as a
   JSON response.

```typescript
import express, { Request, Response } from "express";
import { MongoClient, Db } from "mongodb";

const app = express();
const port = 3000;

const url = "mongodb://localhost:27017";
const dbName = "myDatabase";
const collectionName = "users";

app.get("/users", (req: Request, res: Response) => {
  MongoClient.connect(url, function (err, client) {
    if (err) throw err;
    const db: Db = client.db(dbName);
    const collection = db.collection(collectionName);
    collection.find({}).toArray(function (err, result) {
      if (err) throw err;
      res.json(result);
      client.close();
    });
  });
});

app.listen(port, () => {
  console.log(`Server listening at http://localhost:${port}`);
});
```

2. Write an Express middleware function that logs the IP address and timestamp of each
   incoming request.

```typescript
import express, { Request, Response, NextFunction } from "express";

const app = express();
const port = 3000;

// Middleware function that logs IP address and timestamp of each incoming request
const loggerMiddleware = (req: Request, res: Response, next: NextFunction) => {
  const ip = req.ip;
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${ip} ${req.method} ${req.originalUrl}`);
  next();
};

app.use(loggerMiddleware);

app.get("/", (req: Request, res: Response) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`Server listening at http://localhost:${port}`);
});
```

3. Create an Express route that allows a user to upload a file to the server and save it to a
   specified directory.

```typescript
import express, { Request, Response } from "express";
import fileUpload from "express-fileupload";
import path from "path";

const app = express();
const port = 3000;

app.use(fileUpload());

// Route handler that accepts file uploads and saves them to a specified directory
app.post("/upload", (req: Request, res: Response) => {
  if (!req.files || Object.keys(req.files).length === 0) {
    return res.status(400).send("No files were uploaded.");
  }

  const file = req.files.file as fileUpload.UploadedFile;
  const uploadDir = path.join(__dirname, "uploads");

  file.mv(path.join(uploadDir, file.name), (err) => {
    if (err) {
      console.error(err);
      return res.status(500).send("Error uploading file.");
    }
    res.send("File uploaded!");
  });
});

app.listen(port, () => {
  console.log(`Server listening at http://localhost:${port}`);
});
```

## Part 3: React

1. Create a React component that displays a list of items retrieved from an API endpoint.

```typescript
import React, { useEffect, useState } from "react";

interface Item {
  id: number;
  name: string;
}

const ItemList: React.FC = () => {
  const [items, setItems] = useState<Item[]>([]);

  useEffect(() => {
    fetch("/api/items")
      .then((res) => res.json())
      .then((data) => setItems(data));
  }, []);

  return (
    <div>
      <h2>Item List</h2>
      <ul>
        {items.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
};

export default ItemList;
```

2. Write a React function that takes a number as input and returns a list of that length, with
   each item consisting of a random number between 1 and 100.

```typescript
import React from "react";

interface Props {
  length: number;
}

const RandomNumberList: React.FC<Props> = ({ length }) => {
  const numbers = Array.from(
    { length },
    () => Math.floor(Math.random() * 100) + 1
  );

  return (
    <ul>
      {numbers.map((number, index) => (
        <li key={index}>{number}</li>
      ))}
    </ul>
  );
};

export default RandomNumberList;
```

3. Create a React component that is a widget which is like a contact form (Name, Email,
   Phone)

```typescript
import React, { useState } from "react";

interface ContactFormData {
  name: string;
  email: string;
  phone: string;
}

const ContactForm: React.FC = () => {
  const [formData, setFormData] = useState<ContactFormData>({
    name: "",
    email: "",
    phone: "",
  });

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    console.log(formData);
    // TODO: Send the form data to a server or perform other actions
  };

  const handleInputChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = event.target;
    setFormData((prevFormData) => ({ ...prevFormData, [name]: value }));
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="name">Name:</label>
        <input
          type="text"
          id="name"
          name="name"
          value={formData.name}
          onChange={handleInputChange}
          required
        />
      </div>
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleInputChange}
          required
        />
      </div>
      <div>
        <label htmlFor="phone">Phone:</label>
        <input
          type="tel"
          id="phone"
          name="phone"
          value={formData.phone}
          onChange={handleInputChange}
          required
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  );
};

export default ContactForm;
```

## Part 4: Node.js

1. Write a Node.js script that reads a CSV file and inserts the data into a MongoDB
   collection.

```typescript
import fs from "fs";
import csv from "csv-parser";
import { MongoClient } from "mongodb";

interface Person {
  name: string;
  email: string;
  phone: string;
}

const MONGODB_URI = "mongodb://localhost:27017";
const DB_NAME = "mydb";
const COLLECTION_NAME = "people";

async function main() {
  const client = await MongoClient.connect(MONGODB_URI);
  const db = client.db(DB_NAME);
  const collection = db.collection(COLLECTION_NAME);

  const people: Person[] = [];

  fs.createReadStream("path/to/your/csv/file.csv")
    .pipe(csv())
    .on("data", (row) => {
      const { name, email, phone } = row;
      people.push({ name, email, phone });
    })
    .on("end", async () => {
      await collection.insertMany(people);
      console.log("Data inserted successfully!");
      client.close();
    });
}

main().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

2. Create a Node.js server that allows users to create and delete user accounts, with the
   account information stored in a MongoDB collection.

```typescript
import express from "express";
import { MongoClient, ObjectID } from "mongodb";
import bodyParser from "body-parser";

interface User {
  name: string;
  email: string;
  password: string;
}

const MONGODB_URI = "mongodb://localhost:27017";
const DB_NAME = "mydb";
const COLLECTION_NAME = "users";

async function main() {
  const app = express();
  app.use(bodyParser.json());

  const client = await MongoClient.connect(MONGODB_URI);
  const db = client.db(DB_NAME);
  const collection = db.collection(COLLECTION_NAME);

  app.post("/users", async (req, res) => {
    const user: User = req.body;

    const result = await collection.insertOne(user);
    const insertedUser = result.ops[0];

    res.json(insertedUser);
  });

  app.delete("/users/:id", async (req, res) => {
    const id = new ObjectID(req.params.id);

    const result = await collection.deleteOne({ _id: id });

    if (result.deletedCount === 0) {
      return res.status(404).json({ message: "User not found" });
    }

    res.json({ message: "User deleted successfully" });
  });

  app.listen(3000, () => {
    console.log("Server started on port 3000");
  });
}

main().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

3. Write a Node.js function that takes a string as input and returns the number of words in
   the string.

```typescript
const countWords: number = (input: string) => {
  const words = input.trim().split(/\s+/);
  return words.length;
};
```
