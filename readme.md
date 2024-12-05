# Notes App - Server-Side Documentation

This document provides an overview of the backend implementation for the Notes App. The backend uses **Node.js**, **Express**, **Sequelize**, and **MySQL** as the database. It also integrates several features such as file uploads, user authentication, and CRUD operations for notes.

---

## Table of Contents

1. [Technologies Used](#technologies-used)
2. [Environment Setup](#environment-setup)
3. [Folder Structure](#folder-structure)
4. [Environment Variables](#environment-variables)
5. [Database Models](#database-models)
6. [API Routes](#api-routes)
7. [Middleware](#middleware)
8. [Running the Server](#running-the-server)
9. [Error Handling](#error-handling)

---

## Technologies Used

- **Node.js**: Runtime environment for executing JavaScript on the server.
- **Express**: Web framework for building REST APIs.
- **Sequelize**: ORM for interacting with the MySQL database.
- **MySQL**: Database for storing user and note data.
- **UUID**: For generating unique IDs.
- **dotenv**: For managing environment variables.
- **cors**: To allow cross-origin requests.

---

## Environment Setup

1. Install required dependencies:
   ```bash
   npm install express sequelize mysql2 dotenv cors uuid
   ```
2. Install Sequelize CLI globally:
   ```bash
   npm install -g sequelize-cli
   ```

---

## Folder Structure

```plaintext
notapp_server/
├── config/
│   └── config.json
├── middlewares/
│   ├── AuthMiddleware.js
│   └── upload.js
├── models/
│   ├── index.js
│   ├── Notes.js
│   └── Users.js
├── routes/
│   ├── Notes.js
│   └── Users.js
├── uploads/
│   └── profileImage-[filename]
├── .gitignore
├── package-lock.json
├── package.json
├── server.js
└── .env

```

---

## Environment Variables

Ensure you have a `.env` file with the following variables:
```env
PORT=3001
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=password
DB_NAME=notesdb
JWT_SECRET=your_jwt_secret
```

---

## Database Models

### Users Model
File: `models/Users.js`
```javascript
module.exports = (sequelize, DataTypes) => {
  const Users = sequelize.define("Users", {
    username: { type: DataTypes.STRING, allowNull: false },
    password: { type: DataTypes.STRING, allowNull: false },
    email: { type: DataTypes.STRING, allowNull: false },
    profileImage: { type: DataTypes.STRING, allowNull: true },
  });

  Users.associate = (models) => {
    Users.hasMany(models.Notes, {
      foreignKey: "userId",
      onDelete: "CASCADE",
    });
  };

  return Users;
};
```

### Notes Model
File: `models/Notes.js`
```javascript
const { v4: uuidv4 } = require("uuid");
const getRandomColor = () => {
  const colors = ["#1E90FF", "#FF4500", "#32CD32", "#FFD700"];
  return colors[Math.floor(Math.random() * colors.length)];
};

module.exports = (sequelize, DataTypes) => {
  const Notes = sequelize.define("Notes", {
    id: { type: DataTypes.UUID, defaultValue: uuidv4, primaryKey: true },
    title: { type: DataTypes.STRING, allowNull: false },
    content: { type: DataTypes.STRING, allowNull: false },
    date: { type: DataTypes.DATE, defaultValue: DataTypes.NOW },
    notecolor: { type: DataTypes.STRING, defaultValue: getRandomColor },
    userId: { type: DataTypes.INTEGER, allowNull: false },
  });

  Notes.associate = (models) => {
    Notes.belongsTo(models.Users, {
      foreignKey: "userId",
      onDelete: "CASCADE",
    });
  };

  return Notes;
};
```

---

## API Routes

### Users Routes
File: `routes/Users.js`
Handles user authentication and profile management.

- **POST /auth/register**: Register a new user.
- **POST /auth/login**: Authenticate and log in a user.
- **GET /auth/profile**: Get the profile of the logged-in user.

### Notes Routes
File: `routes/Notes.js`
Handles CRUD operations for notes.

- **POST /notes**: Create a new note.
- **GET /notes**: Fetch all notes for a user.
- **GET /notes/:id**: Fetch a single note by ID.
- **PUT /notes/:id**: Update a note by ID.
- **DELETE /notes/:id**: Delete a note by ID.

---

## Middleware

### File Uploads
- Directory: `uploads/`
- Files are served statically via:
  ```javascript
  const path = require("path");
  app.use("/uploads", express.static(path.join(__dirname, "uploads")));
  ```

### CORS
- Allows requests from the frontend:
  ```javascript
  const cors = require("cors");
  app.use(cors());
  ```

### JSON Parsing
- Parses incoming JSON payloads:
  ```javascript
  app.use(express.json());
  ```

---

## Running the Server

1. Sync the database:
   ```javascript
   db.sequelize.sync();
   ```
2. Start the server:
   ```bash
   node server.js
   ```
3. Server will be running at:
   ```plaintext
   http://localhost:3001
   ```

---

## Error Handling

Ensure proper error handling is implemented in routes and middleware. For example:
```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: "Internal Server Error" });
});
```

---

## Future Improvements

- Implement pagination for fetching notes.
- Introduce validation for user inputs.
- Add logging for better debugging.

--- 

### code with love by
## RabbitDaCoder 