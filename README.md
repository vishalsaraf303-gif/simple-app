{
  "name": "smart-campus",
  "version": "1.0.0",
  "main": "server.js",
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "body-parser": "^1.20.2"
  }
}
{
  "users": [
    { "username": "admin", "password": "admin123" }
  ],
  "announcements": [
    "Welcome to Smart Campus!",
    "Mid-term exams start next week."
  ],
  "attendance": []
}
const express = require("express");
const bodyParser = require("body-parser");
const cors = require("cors");
const fs = require("fs");

const app = express();
app.use(cors());
app.use(bodyParser.json());

const DATA_FILE = "./data.json";

// Login API
app.post("/login", (req, res) => {
  const { username, password } = req.body;
  const data = JSON.parse(fs.readFileSync(DATA_FILE));

  const user = data.users.find(
    u => u.username === username && u.password === password
  );

  if (user) {
    res.json({ success: true });
  } else {
    res.json({ success: false });
  }
});

// Get announcements
app.get("/announcements", (req, res) => {
  const data = JSON.parse(fs.readFileSync(DATA_FILE));
  res.json(data.announcements);
});

app.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});
// Mark attendance
app.post("/attendance", (req, res) => {
  const { name, status } = req.body;
  const data = JSON.parse(fs.readFileSync(DATA_FILE));

  data.attendance.push({ name, status, date: new Date().toDateString() });

  fs.writeFileSync(DATA_FILE, JSON.stringify(data, null, 2));
  res.json({ success: true });
});

// Get attendance
app.get("/attendance", (req, res) => {
  const data = JSON.parse(fs.readFileSync(DATA_FILE));
  res.json(data.attendance);
});
