# Jotihunt Backend

This is the backend for the Jotihunt IRL game. Jotihunt is an interactive, real-world game where participants complete tasks, solve hints, and follow live updates. This backend provides APIs to retrieve new game data (news, hints, and assignments) and enables users to share their live location, which can be displayed to other users.

## Features

- **API for fetching game data**: News, hints, and assignments are fetched from Jotihunt's public API and stored in the local database.
- **Real-time updates**: Game data is automatically updated every minute.
- **Location sharing**: Users can share their live location, which is saved in the database and can be displayed to other players.
- **Visual database**: View a visual representation of the live database.
- **API testing**: Run tests on all API endpoints to ensure proper functionality.

---

## Table of Contents

- [About](#about)
- [Installation](#installation)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
  - [Get Data](#get-data)
  - [Save Location](#save-location)
  - [Get Locations](#get-locations)
  - [Get Content](#get-content)
  - [Get Stats](#get-stats)
  - [Update Item](#update-item)
  - [Visual Database](#visual-database)
  - [Test API Endpoints](#test-api-endpoints)
- [Contributing](#contributing)

---

## About

The Jotihunt Backend provides a set of RESTful APIs to fetch game data and allow users to share their location. The game data includes:

- **News**: General updates related to the game.
- **Hints**: Hints provided during the game that players need to solve.
- **Assignments**: Tasks players need to complete.

Additionally, players can submit their current GPS location to the server, and this information is stored and can be accessed by other players.

---

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/SilkePilon/OpenJotiHuntMap.git
   ```

2. Install the dependencies:

   ```bash
   cd OpenJotiHuntMap
   npm install
   ```

3. Run the server:

   ```bash
   npm start
   ```

4. The server will start at `http://localhost:5000`.

5. Ensure your SQLite databases (`main.db` and `content.db`) are set up correctly. The database schemas are initialized automatically when the server starts.

---

## Usage

Once the server is running, you can interact with the API through `HTTP` requests.

### Node.js Example

Here's how you can retrieve game data (e.g., news) using Node.js:

```javascript
const axios = require("axios");

async function fetchData() {
  try {
    const response = await axios.get("http://localhost:5000/api/data/news");
    console.log("News Data:", response.data);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
}

fetchData();
```

### React Example

Here's how you can display data in a React component:

```javascript
import React, { useState, useEffect } from "react";
import axios from "axios";

const NewsList = () => {
  const [news, setNews] = useState([]);

  useEffect(() => {
    async function fetchNews() {
      try {
        const response = await axios.get("http://localhost:5000/api/data/news");
        setNews(response.data);
      } catch (error) {
        console.error("Error fetching news:", error);
      }
    }

    fetchNews();
  }, []);

  return (
    <div>
      <h2>News</h2>
      <ul>
        {news.map((item) => (
          <li key={item.id}>{item.title}</li>
        ))}
      </ul>
    </div>
  );
};

export default NewsList;
```

---

## API Endpoints

### Get Data

- **Endpoint**: `/api/data/:type`
- **Method**: `GET`
- **Description**: Retrieves game data (news, hints, or assignments).

  - `:type`: Can be one of `news`, `hints`, or `assignments`.

#### Example Request:

```bash
GET http://localhost:5000/api/data/news
```

#### Example Response:

```json
[
  {
    "id": 1,
    "title": "First News",
    "type": "news",
    "publish_at": "2024-09-23T10:00:00Z",
    "retrieved_at": "2024-09-23T10:01:00Z",
    "assignedTo": null,
    "completed": 0,
    "reviewed": 0,
    "points": 0
  }
]
```

### Save Location

- **Endpoint**: `/api/save-location`
- **Method**: `POST`
- **Description**: Allows users to save or update their current location.

#### Example Request:

```bash
POST http://localhost:5000/api/save-location
```

#### Request Body:

```json
{
  "id": "user123",
  "name": "John Doe",
  "description": "At the park",
  "latitude": 52.3676,
  "longitude": 4.9041
}
```

#### Example Response:

```json
{
  "message": "Location created successfully"
}
```

### Get Locations

- **Endpoint**: `/api/get-locations`
- **Method**: `GET`
- **Description**: Retrieves all saved locations from the database.

#### Example Request:

```bash
GET http://localhost:5000/api/get-locations
```

#### Example Response:

```json
[
  {
    "id": "user123",
    "name": "John Doe",
    "description": "At the park",
    "latitude": 52.3676,
    "longitude": 4.9041,
    "timestamp": "2024-09-23T10:01:00Z"
  }
]
```

### Get Content

- **Endpoint**: `/api/content/:id`
- **Method**: `GET`
- **Description**: Retrieves the content message of a specific item by ID.

#### Example Request:

```bash
GET http://localhost:5000/api/content/1
```

#### Example Response:

```json
{
  "message": "This is the content for item 1"
}
```

### Get Stats

- **Endpoint**: `/api/stats`
- **Method**: `GET`
- **Description**: Retrieves general statistics about the game data, such as total items, completed items, and points.

#### Example Request:

```bash
GET http://localhost:5000/api/stats
```

#### Example Response:

```json
{
  "totalItems": 10,
  "itemsByType": {
    "news": 5,
    "hint": 3,
    "assignment": 2
  },
  "completedItems": 4,
  "reviewedItems": 3,
  "totalPoints": 50
}
```

### Update Item

- **Endpoint**: `/api/update/:id`
- **Method**: `PUT`
- **Description**: Updates a specific item's details (assigned user, points, review status, completion status).

#### Example Request:

```bash
PUT http://localhost:5000/api/update/1
```

#### Request Body:

```json
{
  "assignedTo": "User1",
  "points": 10,
  "reviewed": 1,
  "completed": 1
}
```

#### Example Response:

```json
{
  "message": "Item updated successfully",
  "item": {
    "id": 1,
    "title": "First News",
    "type": "news",
    "publish_at": "2024-09-23T10:00:00Z",
    "retrieved_at": "2024-09-23T10:01:00Z",
    "assignedTo": "User1",
    "completed": 1,
    "reviewed": 1,
    "points": 10
  }
}
```

### Visual Database

- **Endpoint**: `/database`
- **Method**: `GET`
- **Description**: Displays a visual representation of the live database using JSON Crack.

#### Example Request:

```bash
GET http://localhost:5000/database
```

This will return an HTML page with an embedded JSON Crack visualization of the database.

### Test API Endpoints

- **Endpoint**: `/api/test`
- **Method**: `GET`
- **Description**: Runs tests on all API endpoints to ensure they are functioning correctly.

#### Example Request:

```bash
GET http://localhost:5000/api/test
```

#### Example Response:

```json
{
  "message": "All endpoints tested",
  "results": {
    "dataEndpoints": {
      "news": { "status": 200, "dataReceived": true },
      "hints": { "status": 200, "dataReceived": true },
      "assignments": { "status": 200, "dataReceived": true }
    },
    "contentEndpoint": { "status": 200, "dataReceived": true },
    "statsEndpoint": { "status": 200, "data": { ... } },
    "updateEndpoint": { "status": 200, "dataReceived": true },
    "locationEndpoints": {
      "saveLocation": { "status": 200, "message": "Location created successfully" },
      "getLocations": { "status": 200, "dataReceived": true }
    }
  }
}
```

---

## Contributing

Feel free to contribute to this project by submitting issues or pull requests.

---

This project is built using `Express.js` and `SQLite3` for managing the backend, while data is fetched from Jotihunt's official API using `Axios`. The backend is designed to automatically retrieve and store game data every minute, allowing for real-time updates during gameplay.
