Project Setup Notes
Folder Structure

```bash

project-root/
│
├── backend/
│   ├── models/
│   │   └── Item.js
│   ├── routes/
│   │   └── items.js
│   ├── server.js
│   ├── .env
│   └── package.json
│
└── frontend/
    ├── pages/
    │   └── index.js
    ├── styles/
    │   └── globals.css
    ├── package.json
    └── next.config.js
```
# Backend Setup
## 1. Create Backend Directory

bash

- mkdir backend
- cd backend

## 2. Initialize the Backend Project

bash

- npm init -y

This will create a package.json file with default settings.
## 3. Install Dependencies

bash

- npm install express mongoose dotenv cors

## 4. Create .env File

- Create a .env file in the backend directory with the following content:

dotenv

- MONGO_URI=mongodb://localhost:27017/mydatabase

## 5. Create Project Files

server.js

```javascript

const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const itemRoutes = require('./routes/items');
require('dotenv').config();

const app = express();
const port = process.env.PORT || 5000;

// Enable CORS
app.use(cors());

// Connect to MongoDB
mongoose.connect(process.env.MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
})
  .then(() => console.log('MongoDB connected'))
  .catch((err) => console.error('MongoDB connection error:', err));

// Middleware to parse JSON
app.use(express.json());

// Routes
app.use('/api/items', itemRoutes);

// Start the server
app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```


models/Item.js

```javascript

const mongoose = require('mongoose');

const itemSchema = new mongoose.Schema({
  name: { type: String, required: true },
});

const Item = mongoose.model('Item', itemSchema);

module.exports = Item;

routes/items.js
```

```javascript

const express = require('express');
const router = express.Router();
const Item = require('../models/Item');

// Get all items
router.get('/', async (req, res) => {
  try {
    const items = await Item.find();
    res.json(items);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});
```
```javascript

// Create a new item
router.post('/', async (req, res) => {
  const item = new Item({
    name: req.body.name,
  });

  try {
    const newItem = await item.save();
    res.status(201).json(newItem);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
});

module.exports = router;

```

## 6. Start the Backend Server

bash

- npm start

# Frontend Setup
1. Create Frontend Directory

bash

- mkdir frontend
- cd frontend

## 2. Initialize the Frontend Project

bash

-  create-next-app@latest .

This will set up a new Next.js project in the current directory.
## 3. Install Additional Dependencies

bash

- npm install axios

## 4. Create Project Files

pages/index.js

``` javascript

import { useEffect, useState } from 'react';
import axios from 'axios';

const Home = () => {
  const [items, setItems] = useState([]);

  useEffect(() => {
    const fetchItems = async () => {
      try {
        const response = await axios.get('http://localhost:5000/api/items');
        setItems(response.data);
      } catch (error) {
        console.error('Error fetching items:', error);
      }
    };

    fetchItems();
  }, []);

  return (
    <div>
      <h1>Items</h1>
      <ul>
        {items.length > 0 ? (
          items.map(item => (
            <li key={item._id}>{item.name}</li>
          ))
        ) : (
          <li>No items available</li>
        )}
      </ul>
    </div>
  );
};

export default Home;
```
styles/globals.css

```css

body {
  font-family: Arial, sans-serif;
}
```
next.config.js

```javascript

/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
};

module.exports = nextConfig;
```
## 5. Start the Frontend Development Server

bash

- npm run dev

## Running the Application
1. Start the Backend Server

bash

- cd backend
- npm start

## 2. Start the Frontend Development Server

bash

- cd ../frontend
- npm run dev

## 3. Access the Application

    Frontend Application: Visit http://localhost:3000 in your browser.
    Backend Server: Running on http://localhost:5000 (default port; change if needed).

Notes

    Ensure both backend and frontend servers are running concurrently.
    Update the MongoDB URI in the .env file as needed if using a cloud-based MongoDB service.
    Handle CORS issues if frontend and backend are on different ports by using the cors package in Express.