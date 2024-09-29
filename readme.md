# Jotihunt Backend

This is the backend for the Jotihunt IRL game. Jotihunt is an interactive, real-world game where participants complete tasks, solve hints, and follow live updates. This backend provides APIs to retrieve new game data (news, hints, and assignments) and enables users to share their live location, which can be displayed to other users.

## Features

- **API for fetching game data**: News, hints, and assignments are fetched from Jotihunt's public API and stored in the local database.
- **Real-time updates**: Game data is automatically updated every minute.
- **Location sharing**: Users can share their live location, which is saved in the database and can be displayed to other players.
- **Visual database**: View a visual representation of the live database.
- **API testing**: Run tests on all API endpoints to ensure proper functionality.
- **Performance monitoring**: Track response times for both Jotihunt API and our own API endpoints.
- **AI-generated plans**: Generate plans for solving hints and assignments using AI.
- **Live leaderboard**: View the positions of competing groups by points.

## Table of Contents

- [About](#about)
- [Installation](#installation)
- [Frontend Examples & Recommendations](#frontend-examples--recommendations)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
  - [Get Data](#get-data)
  - [Save Location](#save-location)
  - [Get Locations](#get-locations)
  - [Get Content](#get-content)
  - [Get Stats](#get-stats)
  - [Update Item](#update-item)
  - [Generate Plan](#generate-plan)
  - [Get Leaderboard](#get-leaderboard)
  - [Get Area Statuses](#get-area-statuses)
  - [Get Area Status History](#get-area-status-history)
  - [Visual Database](#visual-database)
  - [Test API Endpoints](#test-api-endpoints)
  - [Get API Response Times](#get-api-response-times)
  - [View Response Time Graph](#view-response-time-graph)
- [Contributing](#contributing)

## About

The Jotihunt Backend provides a set of RESTful APIs to fetch game data and allow users to share their location. The game data includes:

- **News**: General updates related to the game.
- **Hints**: Hints provided during the game that players need to solve.
- **Assignments**: Tasks players need to complete.
- **Area Statuses**: Current status of different areas in the game.

Additionally, players can submit their current GPS location to the server, and this information is stored and can be accessed by other players.

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/SilkePilon/OpenJotiHuntMap.git
   ```

2. Install the dependencies:

   ```bash
   cd OpenJotiHuntMap
   cd backend
   npm install
   ```

3. Set up environment variables:
   Create a `.env` file in the root directory or rename `.env.example` to `.env` and add/change the following:

   ```
   NVIDIA_API_KEY=your_nvidia_api_key_here
   PORT=5000
   ```

4. Run the server:

   ```bash
   npm start
   ```

5. The server will start at `http://localhost:5000`.
6. Ensure your SQLite databases (`main.db`) are set up correctly. The database schemas are initialized automatically when the server starts.

## Frontend Examples & Recommendations

Under the `examples/` directory you can find some simple examples that make full use of the API or you can see the demo (without API running) [here](https://silkepilon.github.io/OpenJotiHuntMap/example.html) Note these examples were made fast and are only made to visualize a use case for the API and we don't recommend using this for the game. We instead encourage you and your group to build your own beautiful dashboard using modern tools.

## Database Schema

The backend uses SQLite databases with the following main tables:

1. **items**: Stores game items (news, hints, assignments)

   - Columns: id, title, type, publish_at, retrieved_at, assignedTo, completed, reviewed, points

2. **content**: Stores the content of game items

   - Columns: id, message

3. **locations**: Stores user-shared locations

   - Columns: id, name, description, latitude, longitude, timestamp

4. **current_area_statuses**: Stores the current status of each area

   - Columns: name, status, last_updated

5. **area_status_history**: Stores the history of area status changes

   - Columns: id, area_id, status, timestamp

6. **jotihunt_api_response_times**: Tracks response times of the Jotihunt API

   - Columns: id, timestamp, response_time_ms

7. **our_api_response_times**: Tracks response times of our own API endpoints

   - Columns: id, endpoint, timestamp, response_time_ms

8. **plans**: Stores AI-generated plans for hints and assignments

   - Columns: id, item_id, item_title, plan_content, created_at

These tables are automatically created and managed by the backend application.

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
  "content": "This is the content for item 1"
}
```

### Get Area Statuses

- **Endpoint**: `/api/area-statuses`
- **Method**: `GET`
- **Description**: Retrieves the current status of all areas in the game.

#### Example Request:

```bash
GET http://localhost:5000/api/area-statuses
```

#### Example Response:

```json
[
  {
    "name": "Alpha",
    "status": "red",
    "last_updated": "2024-09-23T10:00:00Z"
  },
  {
    "name": "Beta",
    "status": "green",
    "last_updated": "2024-09-23T09:45:00Z"
  }
]
```

### Get Area Status History

- **Endpoint**: `/api/area-status-history/:areaName`
- **Method**: `GET`
- **Description**: Retrieves the status history of a specific area.

  - `:areaName`: Can be one of `Alpha`, `Bravo`, `Charlie`, `Delta`, `Echo` or `Foxtrot`.

#### Example Request:

```bash
GET http://localhost:5000/api/area-status-history/Alpha
```

#### Example Response:

```json
[
  {
    "id": 1,
    "area_id": "Alpha",
    "status": "green",
    "timestamp": "2024-09-23T10:00:00Z"
  },
  {
    "id": 2,
    "area_id": "Alpha",
    "status": "red",
    "timestamp": "2024-09-23T09:30:00Z"
  }
]
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

### Generate Plan

- **Endpoint**: `/api/generate-plan/:id`
- **Method**: `GET`
- **Description**: Generates an AI-powered plan for solving a hint or assignment.

#### Example Request:

```bash
GET http://localhost:5000/api/generate-plan/1
```

#### Example Response:

```json
{
  "message": "Plan generated and saved successfully",
  "plan": "Step 1: ... Step 2: ... Step 3: ..."
}
```

### Get Leaderboard

- **Endpoint**: `/api/leaderboard`
- **Method**: `GET`
- **Description**: Scrapes the leaderboard data from Jotihunt's public scorelist and returns it in JSON format.
- **Optional**: Enter a group name like this `/api/leaderboard/groupNameHere` to specifically search for that group. The name does not need to be correct.
  - `:groupName`: Is one of type `string`.

#### Example Request:

```bash
GET http://localhost:5000/api/leaderboard
```

#### Example Response:

```json
[
  {
    "position": 1,
    "groupName": "Team A",
    "points": 250
  },
  {
    "position": 2,
    "groupName": "Team B",
    "points": 230
  },
  {
    "position": 3,
    "groupName": "Team C",
    "points": 220
  }
]
```

#### Example Request:

```bash
GET http://localhost:5000/api/leaderboard/alexander
```

#### Example Response:

```json
{
  "position": 21,
  "groupName": "alexandergroep",
  "points": 328
}
```

#### Error Response:

If the server fails to scrape the leaderboard, the following error response will be returned:

```json
{
  "error": "Failed to scrape leaderboard",
  "details": "Error message"
}
```

This will provide users with the necessary information on how to use the new `/api/leaderboard` endpoint.

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

### Get API Response Times

- **Endpoint**: `/api/response-times`
- **Method**: `GET`
- **Description**: Retrieves the response times for both the Jotihunt API and our own API endpoints.

#### Example Request:

```bash
GET http://localhost:5000/api/response-times
```

#### Example Response:

```json
{
  "jotihuntApiTimes": [
    {
      "id": 1,
      "timestamp": "2024-09-23T10:01:00Z",
      "response_time_ms": 250.5
    }
  ],
  "ourApiTimes": [
    {
      "id": 1,
      "endpoint": "/api/data/news",
      "timestamp": "2024-09-23T10:02:00Z",
      "response_time_ms": 15.3
    }
  ]
}
```

### View Response Time Graph

- **Endpoint**: `/api/response-time-graph`
- **Method**: `GET`
- **Description**: Displays a graph of API response times for both Jotihunt API and our own API.

#### Example Request:

```bash
GET http://localhost:5000/api/response-time-graph
```

This will return an HTML page with an embedded chart showing the response times over time.

## Contributing

Feel free to contribute to this project by submitting issues or pull requests.

---

This project is built using `Express.js` and `SQLite3` for managing the backend, while data is fetched from Jotihunt's official API using `Axios`. The backend is designed to automatically retrieve and store game data every minute, allowing for real-time updates during gameplay. It also includes performance monitoring features to track API response times and AI-powered plan generation using the NVIDIA AI API.
