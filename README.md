const express = require("express");
const sqlite3 = require("sqlite3").verbose();
const bodyParser = require("body-parser");
const cors = require("cors");

const app = express();
app.use(cors());
app.use(bodyParser.json());

const db = new sqlite3.Database("./database.db");

// Create table
db.run(`
  CREATE TABLE IF NOT EXISTS complaints (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    email TEXT,
    message TEXT
  )
`);

// Submit complaint
app.post("/submit", (req, res) => {
  const { name, email, message } = req.body;
  db.run(
    "INSERT INTO complaints (name, email, message) VALUES (?, ?, ?)",
    [name, email, message],
    () => res.send({ status: "Complaint submitted" })
  );
});

// Get all complaints (Admin)
app.get("/complaints", (req, res) => {
  db.all("SELECT * FROM complaints", [], (err, rows) => {
    res.json(rows);
  });
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
// Admin Login
app.post("/admin-login", (req, res) => {
  const { username, password } = req.body;

  if (username === "admin" && password === "admin123") {
    res.send({ success: true });
  } else {
    res.send({ success: false });
  }
});
