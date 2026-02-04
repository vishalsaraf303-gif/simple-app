{
  "name": "smart-campus",
  "version": "1.0.0",
  "main": "server.js",
  "dependencies": {
    "express": "^4.18.2",
    "sqlite3": "^5.1.6",
    "cors": "^2.8.5"
  }
}
const express = require("express");
const sqlite3 = require("sqlite3").verbose();
const cors = require("cors");

const app = express();
const PORT = 3000;

app.use(cors());
app.use(express.json());

// Database
const db = new sqlite3.Database("database.db");

// Create table
db.run(`
  CREATE TABLE IF NOT EXISTS students (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    department TEXT,
    year TEXT
  )
`);

// Add student
app.post("/add-student", (req, res) => {
  const { name, department, year } = req.body;
  db.run(
    "INSERT INTO students (name, department, year) VALUES (?, ?, ?)",
    [name, department, year],
    () => res.send({ message: "Student added successfully" })
  );
});

// Get students
app.get("/students", (req, res) => {
  db.all("SELECT * FROM students", [], (err, rows) => {
    res.send(rows);
  });
});

// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
