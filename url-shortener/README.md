# URL Shortener

- Design a URL shortener service that takes a valid URL and returns a shortened URL, redirecting the user to the previously provided URL.

- Also keep track of the total views/clicks on the URL

## Routes

**POST /URL** - Generates a new short URL and returns the shortened URL in the format **_example.com/random-id_**

**GET /:id** - Redirects the user to the original URL

**GET /URL/analytics/:id** - Returns the clicks for the provided short id

# Architecture and Dependencies

**_nanoid_** - Url Shortener Service


In this project, you will build a URL shortener using **Node.js**, **Express**, and **MongoDB**. Below is the code for the main files created throughout the video.

### 4. `routes/url.js` (Router)

javascript
const express = require("express");
const { handleGenerateNewShortURL, handleGetAnalytics } = require("../controllers/url");
const router = express.Router();

router.post("/", handleGenerateNewShortURL);
router.get("/analytics/:shortId", handleGetAnalytics);

module.exports = router;

### 5. `index.js` (Server Setup)

The main entry point to start your server and link routes.
javascript
const express = require("express");
const { connectToMongoDB } = require("./connect");
const urlRoute = require("./routes/url");
const URL = require("./models/url");
const app = express();
const PORT = 8001;

connectToMongoDB("mongodb://127.0.0.1:27017/short-url").then(() =\>
console.log("MongoDB connected")
);

app.use(express.json());
app.use("/url", urlRoute);

app.get("/:shortId", async (req, res) =\> {
const shortId = req.params.shortId;
const entry = await URL.findOneAndUpdate(
{ shortId },
{ $push: { visitHistory: { timestamp: Date.now() } } }
);
res.redirect(entry.redirectURL);
});

app.listen(PORT, () =\> console.log(`Server started at PORT:${PORT}`));
