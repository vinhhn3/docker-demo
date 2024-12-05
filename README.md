Dockerizing a Node.js application with MongoDB involves creating a containerized environment for both your application and the database. Below is a step-by-step guide:

---

### **Step 1: Prepare the Node.js Application**

Ensure your Node.js application has a proper structure and a `package.json` file.

**Example: `app.js`**

```javascript
const express = require('express');
const mongoose = require('mongoose');

const app = express();
app.use(express.json());

// MongoDB connection
const dbUri = process.env.MONGO_URI || "mongodb://localhost:27017/mydb";
mongoose.connect(dbUri, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('Connected to MongoDB'))
    .catch(err => console.error('Could not connect to MongoDB:', err));

// Example route
app.get('/', (req, res) => {
    res.send('Hello, World!');
});

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

**Install Dependencies**

```bash
npm init // Init the application
npm install express mongoose // Install dependencies
```

---

### **Step 2: Create a `Dockerfile`**

The `Dockerfile` defines the image for the Node.js application.

**`Dockerfile`**

```dockerfile
# Use Node.js official image
FROM node:18

# Set working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the application port
EXPOSE 3000

# Define the command to run the app
CMD ["node", "app.js"]
```

---

### **Step 3: Create a `docker-compose.yml` File**

The `docker-compose.yml` sets up multiple services: the Node.js app and MongoDB.

**`docker-compose.yml`**

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      MONGO_URI: "mongodb://mongo:27017/mydb"
    depends_on:
      - mongo

  mongo:
    image: mongo:6.0
    container_name: mongodb
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

---

### **Step 4: Build and Run Containers**

Run the following commands in your terminal:

1. **Build the containers:**

   ```bash
   docker-compose build
   ```
2. **Start the services:**

   ```bash
   docker-compose up
   ```
3. **Access the app:** Open http://localhost:3000 in your browser.

---

### **Step 5: Verify MongoDB Connection**

You can use a tool like MongoDB Compass or run a shell into the MongoDB container:

```bash
docker exec -it mongodb mongosh
```

Run:

```javascript
show dbs
```

You should see `mydb` created after running the Node.js application.

---

### **Step 6: Optional - Add `.dockerignore`**

To optimize the Docker image, add a `.dockerignore` file to exclude unnecessary files.

**Example: `.dockerignore`**

```lua
node_modules
npm-debug.log
.DS_Store
```

---

This setup ensures your Node.js application is containerized and works seamlessly with MongoDB. You can now deploy these containers to any Docker-compatible environment.
