<h1>Code Breakdown</h1>

<p>This page should cover all the steps that were taken to create the MyMovieList web application. Code snippets will be shown and explained, along with screenshots of how the database on PostgreSQL was used with different tables, analyzing how the database connects to the website in the localhost, and how users have their own lists with help of JWT.</p>

<h2>Database</h2>

<p>
The website uses one database with three tables. The tables are called 'users', 'available_movies' and 'movies'. 
</p>

<p align="center">
  <kbd><img src="https://i.imgur.com/im6rlKP.png" alt="AddingItem" width="800px"></kbd>
</p>

<p>The role of the 'users' table is to store the account of users of the website, saving their unique passwords while encrypting them inside the database, along with their user_id. The information that is stored unchanged are 'user_name' and 'user_email', which are used for logging in and registering. This step is essential for the functionality of the website as each user must have their own movie list, and may only access their own MyMovieList account.</p>

<p align="center">
  <kbd><img src="https://i.imgur.com/j0UWyIB.png" alt="AddingItem" width="400px"></kbd>
</p>

<p>The role of the 'available_movies' table is to have all the possible movies the user can add to their movie list. I had the idea to find movie title datasets in Kaggle and populate the available_movies table with all those titles because I needed a way to impede users from adding just anything to the list. The only thing users should be allowed to add to their lists are existing movies. I knew I could not populate the whole table manually seeing that the dataset had more than 24,000 entries, so I used the command "COPY available_movies (title) FROM '/path/to/movies.csv' DELIMITER ',' CSV;" to copy all the tuples from the dataset to available_movies.
</p>

<p align="center">
  <kbd><img src="https://i.imgur.com/c76cdwa.png" alt="AddingItem" width="600px"></kbd>
</p>

<p>The last table I had to create and the most crucial to the idea of the website was the 'movies' table. This table stores the movie title selected from the 'available_movies' table, the rating the user gave to said movie, and the user_id of the user that selection belong to. This is the table that connects 'users' and 'available_movies', making everything possible.
</p>


<h2>Server</h2>

<h3>index.js</h3>

<p>
This code sets up an Express.js server with middleware for parsing JSON requests and enabling CORS. It imports and mounts several route handlers: jwtAuthRouter for handling authentication under /auth, dashboardRouter for user-specific operations under /dashboard, and movieRoutesRouter and suggestionsRouter for managing movie-related routes and suggestions, also under /dashboard. By organizing routes this way, the application maintains clear separation of concerns and modularity. The server listens on port 5001, ready to handle incoming requests.
</p>

<h3>suggestions.js</h3>

<p>
This code defines a route in an Express.js application for fetching movie title suggestions based on a user's search query. It uses a PostgreSQL database to store available movie titles and performs a case-insensitive search to match the query against the titles in the database. The route handler retrieves the query from the request, converts it to lowercase, and executes a parameterized SQL query to find matching titles using the LIKE operator. The results are then extracted and sent back as a JSON response. Error handling is implemented to log any issues and return a 500 status code if an error occurs. The router is exported for integration into the main application.
</p>

<h3>movieRoutes.js</h3>

<p>
This code defines a set of RESTful API endpoints for managing a user's movie list within an Express.js application, using PostgreSQL for database interactions and JWT for authentication. It includes routes to get all movies (GET /), add a new movie (POST /), update an existing movie (PUT /:id), and delete a movie (DELETE /:id), each protected by an authorization middleware to ensure only authenticated users can perform these actions. The database queries are parameterized to prevent SQL injection, and error handling is implemented to log errors and respond with a 500 status code in case of server issues. This setup ensures secure and efficient management of user-specific movie data.
</p>

<h3>dashboard.js</h3>

<p>This code sets up an Express.js router to handle user-related and movie-related API endpoints, utilizing PostgreSQL for database operations and JWT for user authentication. The main route (GET /) retrieves the authenticated user's name from the database using their user ID, which is extracted from the JWT payload by an authorization middleware. If the query is successful, it returns the user's name; otherwise, it logs the error and responds with a 500 status code. Additionally, the router integrates another set of routes (/movies) from a separate movieRoutes module, effectively modularizing the application and keeping the user-specific and movie-specific routes organized and maintainable.</p>

<h2>Client</h2>

<h3>Dashboard.js</h3>

<p>
This React component, Dashboard, serves as the user interface for authenticated users, displaying a personalized welcome message and providing logout functionality. It utilizes React hooks to manage state (useState) and side effects (useEffect). The getName function fetches the authenticated user's name from the server using a token stored in localStorage, while the logout function handles user logout by removing the token and updating the authentication state. The component's structure includes a header with a welcome message and logout button, and it integrates a Movie component to manage and display the user's movies, ensuring a cohesive and interactive user experience. The Dashboard component encapsulates its elements within a Fragment, ensuring that adjacent JSX elements are rendered without an additional DOM node. This avoids unnecessary div wrappers and helps maintain a cleaner and more concise JSX structure. By utilizing Fragment, the component efficiently organizes its content while enhancing readability and minimizing the impact on the rendered output.
</p>

<h3>Movie.js</h3>

<p>
The Movie component in React provides an interface for managing a user's movie list, offering functionalities to view, add, edit, and delete movies. It uses state hooks to manage data such as the list of movies, input fields for titles and ratings, and suggestions for movie titles. When the component mounts, it fetches the user's movies from the backend. The component allows users to edit movie ratings directly within the list and save the changes, or delete movies entirely. Additionally, it features an Autosuggest input for adding new movies, which helps users find and select movie titles easily. This component integrates several helper functions to handle suggestions and star ratings, creating an efficient and user-friendly experience for managing movie collections.
</p>

<h3>AddMovie.js</h3>

<p>
The addMovie function is an asynchronous utility for adding a new movie to a user's movie list in a React application. It ensures that there are no duplicate movie titles (case-insensitive) in the list before proceeding. If a duplicate is found, it displays an error message using react-toastify. The function sends a POST request to the server with the new movie's title and rating, using the token stored in localStorage for authentication. Upon successful addition, it updates the movies state with the newly added movie, resets the input fields, clears the selected suggestion, and shows a success message. This function encapsulates the logic for adding a movie, maintaining clean and organized code within the Movie component.
</p>

<h3>DeleteMovie.js</h3>

<p>
The deleteMovie function is an asynchronous utility designed to remove a movie from a user's list in a React application. It sends a DELETE request to the server, identifying the movie by its ID, and uses the token from localStorage for authentication. Upon successful deletion, it updates the local state by filtering out the deleted movie from the current list and displays a success message using react-toastify. This function helps keep the main component code clean and focused by encapsulating the deletion logic.
</p>

<h3>EditMovie.js</h3>

<p>
The editMovie function is an asynchronous utility responsible for updating the rating of a movie in a user's list within a React application. It ensures that the provided rating falls within the range of 1 to 5; otherwise, it displays an error message using react-toastify. Upon validation, the function constructs a request body with the new rating and sends a PUT request to the server, identifying the movie by its ID. It updates the local state with the modified movie rating upon successful completion, clears the current edit ID, and displays a success message. By encapsulating the edit logic, this function contributes to maintaining a clear and concise structure within the main component.
</p>

<h3>renderStars.js</h3>

<p>
The renderStars function is a simple utility designed to render star ratings based on a numeric rating value within a React application. It takes a rating parameter, representing the number of filled stars, and returns a JSX element containing filled and empty star characters. The number of filled stars corresponds to the rating value, while the remaining stars are represented as empty. This function provides a visual representation of ratings in the form of stars, enhancing the user experience when displaying movie ratings or similar data.
</p>

<h3>suggestions.js</h3>

<p>
The suggestions.js file encompasses a collection of utility functions tailored for managing movie title suggestions within a React application. These functions facilitate communication with the backend API to retrieve and present suggestions based on user input. getSuggestions orchestrates the asynchronous fetching of movie title suggestions, limiting the results to the first 5, and updating the suggestions state accordingly. onChange updates the title state with the new input value and resets the selected suggestion as the user types. onSuggestionsFetchRequested triggers the retrieval of suggestions when the input value changes, while onSuggestionsClearRequested handles clearing suggestions when the input field is emptied. The renderSuggestion function defines the appearance of individual suggestions in the dropdown, while getSuggestionValue ensures the correct value is used when a suggestion is selected. Finally, getSuggestionsContainer manages the layout and styling of the suggestions container for a cohesive user interface. Collectively, these utility functions contribute to an intuitive and efficient mechanism for suggesting movie titles as users interact with the input field.
</p>

<hr>

### <h3>index.js</h3>

<details>
<summary>Click to expand code</summary>

```js

const express = require("express");
const app = express();
const cors = require("cors");
const pool = require("./db");

// Middleware
app.use(express.json());
app.use(cors());

// Import route handlers
const jwtAuthRouter = require("./routes/jwtAuth");
const dashboardRouter = require("./routes/dashboard");
const movieRoutesRouter = require("./routes/movieRoutes");
const suggestionsRouter = require("./routes/suggestions"); // Import the suggestions router

// Mount route handlers
app.use("/auth", jwtAuthRouter);
app.use("/dashboard", dashboardRouter);
app.use("/dashboard", movieRoutesRouter);
app.use("/dashboard", suggestionsRouter); // Mount the suggestions router under the '/dashboard' path

app.listen(5001, () => {
    console.log("Server is running on port 5001");
});


```
</details>

<hr>

### <h3>suggestions.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// Import necessary modules
const express = require('express');
const router = express.Router();
const pool = require('../db'); // Assuming you have a pool for database connections

// Route handler for fetching movie title suggestions
router.get('/movies/suggestions', async (req, res) => {
  try {
    // Get the search query from the request query parameters
    const query = req.query.query.toLowerCase(); // Convert to lowercase for case-insensitive search

    // Query the available_movies table for movie titles matching the search query
    const response = await pool.query(
      `SELECT title FROM available_movies WHERE LOWER(title) LIKE $1`,
      [`%${query}%`] // Use LIKE operator for substring search
    );

    // Extract movie titles from the database response
    const suggestions = response.rows.map((row) => row.title);

    // Send the list of movie title suggestions as JSON response
    res.json(suggestions);
  } catch (err) {
    console.error(err.message);
    res.status(500).send('Server Error');
  }
});

// Export the router to use in the main application
module.exports = router;

```
</details>

<hr>

### <h3>movieRoutes.js</h3>

<details>
<summary>Click to expand code</summary>

```js

const router = require("express").Router();
const pool = require("../db");
const authorization = require("../middleware/authorization");

// Get all movies for a user
router.get("/", authorization, async (req, res) => {
  try {
    const user_id = req.user;

    const movies = await pool.query("SELECT * FROM movies WHERE user_id = $1", [
      user_id,
    ]);

    res.json(movies.rows);
  } catch (err) {
    console.error(err.message);
    res.status(500).send("Server Error");
  }
});

// Add a new movie
router.post("/", authorization, async (req, res) => {
  try {
    const { title, rating } = req.body;
    const user_id = req.user;

    const newMovie = await pool.query(
      "INSERT INTO movies (title, rating, user_id) VALUES ($1, $2, $3) RETURNING *",
      [title, rating, user_id]
    );

    res.json(newMovie.rows[0]);
  } catch (err) {
    console.error(err.message);
    res.status(500).send("Server Error");
  }
});

// Update a movie
router.put("/:id", authorization, async (req, res) => {
  try {
    const { id } = req.params;
    const { title, rating } = req.body;
    const user_id = req.user;

    const updatedMovie = await pool.query(
      "UPDATE movies SET title = $1, rating = $2 WHERE movie_id = $3 AND user_id = $4 RETURNING *",
      [title, rating, id, user_id]
    );

    if (updatedMovie.rows.length === 0) {
      return res.status(404).json("Movie not found or unauthorized");
    }

    res.json(updatedMovie.rows[0]);
  } catch (err) {
    console.error(err.message);
    res.status(500).send("Server Error");
  }
});

// Delete a movie
router.delete("/:id", authorization, async (req, res) => {
  try {
    const { id } = req.params;
    const user_id = req.user;

    const deletedMovie = await pool.query(
      "DELETE FROM movies WHERE movie_id = $1 AND user_id = $2 RETURNING *",
      [id, user_id]
    );

    if (deletedMovie.rows.length === 0) {
      return res.status(404).json("Movie not found or unauthorized");
    }

    res.json("Movie deleted successfully");
  } catch (err) {
    console.error(err.message);
    res.status(500).send("Server Error");
  }
});

module.exports = router;

```
</details>

<hr>


### <h3>(Server) dashboard.js</h3>

<details>
<summary>Click to expand code</summary>

```js

const router = require("express").Router();
const pool = require("../db");
const authorization = require('../middleware/authorization');

router.get("/", authorization, async(req, res) => {
    try {
        //req.user has the payload
        //res.json(req.user);

        const user = await pool.query("SELECT user_name FROM users WHERE user_id = $1", [req.user]);

        res.json(user.rows[0]);
    } catch (err) {
        console.error(err.message)
        res.status(500).json("Server Error");
    }
});

router.use("/movies", require("./movieRoutes"));

module.exports = router;


```
</details>

<hr>

### <h3>(client)Dashboard.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// Dashboard.js
import React, { Fragment, useState, useEffect } from 'react';
import { toast } from 'react-toastify';
import Movie from './Movie';

const Dashboard = ({ setAuth }) => {
  const [name, setName] = useState('');

  const getName = async () => {
    try {
      const response = await fetch('http://localhost:5001/dashboard/', {
        method: 'GET',
        headers: { token: localStorage.token },
      });
      const parseRes = await response.json();
      setName(parseRes.user_name);
    } catch (err) {
      console.error(err.message);
    }
  };

  const logout = (e) => {
    e.preventDefault();
    localStorage.removeItem('token');
    setAuth(false);
    toast.success('Logged out successfully!');
  };

  useEffect(() => {
    getName();
  }, []);

  return (
    <Fragment>
      <div className="container dashboard-header bg-dark">
        
          <div className="row">
            <div className="col">
              <h1 className="text-light">Welcome {name}!</h1>
            </div>
            <div className="col text-right">
              <button className="btn btn-primary logout-btn" onClick={(e) => logout(e)}>
                Logout
              </button>
            </div>
          </div>
        </div>
      
      <div className="container">
        <div className="movies-section">
          <Movie />
        </div>
      </div>
    </Fragment>
  );
};

export default Dashboard;


```
</details>

<hr>

### <h3>Movie.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// Movie.js
import React, { useState, useEffect } from 'react';
import Autosuggest from 'react-autosuggest';
// import { toast } from 'react-toastify';
import addMovie from './AddMovie'; // Import the addMovie function
import editMovie from './EditMovie'; // Import the editMovie function
import deleteMovie from './DeleteMovie'; // Import the deleteMovie function
import renderStars from './renderStars'; // Import the renderStars function
import {
  onChange,
  onSuggestionsFetchRequested,
  onSuggestionsClearRequested,
  renderSuggestion,
  getSuggestionValue,
  getSuggestionsContainer
} from './suggestions'; // Import suggestion functions

const Movie = () => {
  const [movies, setMovies] = useState([]);
  const [title, setTitle] = useState('');
  const [rating, setRating] = useState('');
  const [editId, setEditId] = useState(null);
  const [newMovieRating, setNewMovieRating] = useState('');
  const [suggestions, setSuggestions] = useState([]);
  const [selectedSuggestion, setSelectedSuggestion] = useState(null);

  useEffect(() => {
    getMovies();
  }, []);

  const getMovies = async () => {
    try {
      const response = await fetch('http://localhost:5001/dashboard/movies', {
        method: 'GET',
        headers: { token: localStorage.token },
      });
      const parseRes = await response.json();
      setMovies(parseRes);
    } catch (err) {
      console.error(err.message);
    }
  };

  const handleEdit = (id) => {
    setEditId(id);
    const movieToEdit = movies.find((movie) => movie.movie_id === id);
    setRating(movieToEdit.rating);
  };

  return (
    <div className="movie-list">
      <table className="table table-dark">
        <thead>
          <tr className="bg-primary">
            <th scope="col">Title</th>
            <th scope="col">Rating</th>
            <th scope="col">Actions</th>
          </tr>
        </thead>
        <tbody>
          {movies.map((movie) => (
            <tr key={movie.movie_id}>
              <td>{movie.title}</td>
              <td>
                {editId === movie.movie_id ? (
                  <input
                    type="number"
                    value={rating}
                    onChange={(e) => {
                      const rating = parseInt(e.target.value);
                      if (!isNaN(rating)) {
                        setRating(Math.min(Math.max(rating, 1), 5));
                      } else {
                        setRating('');
                      }
                    }}
                    className="form-control"
                  />
                ) : (
                  <div onClick={() => handleEdit(movie.movie_id)}>
                    {renderStars(movie.rating)}
                  </div>
                )}
              </td>
              <td>
                {editId === movie.movie_id ? (
                  <button
                    className="btn btn-success mr-2"
                    onClick={() => editMovie(movie.movie_id, rating, movies, setMovies, setEditId)}
                  >
                    Save
                  </button>
                ) : (
                  <button
                    className="btn btn-warning mr-2"
                    onClick={() => {
                      setRating(movie.rating);
                      setEditId(movie.movie_id);
                    }}
                  >
                    Edit
                  </button>
                )}
                <button
                  className="btn btn-danger"
                  onClick={() => deleteMovie(movie.movie_id, movies, setMovies)}
                >
                  Delete
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
      <div className="add-movie">
        <h2>Add New Movie</h2>
        <Autosuggest
          suggestions={suggestions}
          onSuggestionsFetchRequested={(value) => onSuggestionsFetchRequested(value, setSuggestions)}
          onSuggestionsClearRequested={() => onSuggestionsClearRequested(setSuggestions)}
          getSuggestionValue={getSuggestionValue}
          renderSuggestion={renderSuggestion}
          inputProps={{
            placeholder: 'Movie Title',
            value: title,
            onChange: (event, { newValue }) => onChange(event, { newValue }, setTitle, setSelectedSuggestion),
            className: "form-control"
          }}
          onSuggestionSelected={(event, { suggestion }) => setSelectedSuggestion(suggestion)}
          suggestionsContainer={getSuggestionsContainer}
        />
        <input
          type="number"
          placeholder="Rating"
          value={newMovieRating}
          onChange={(e) => {
            const rating = parseInt(e.target.value);
            if (!isNaN(rating)) {
              setNewMovieRating(Math.min(Math.max(rating, 1), 5));
            } else {
              setNewMovieRating('');
            }
          }}
          className="form-control"
        />
        <button
          className="btn btn-primary mt-3"
          onClick={() => addMovie(title, newMovieRating, movies, setMovies, setTitle, setNewMovieRating, setSelectedSuggestion)}
          disabled={!selectedSuggestion}
        >
          Add Movie
        </button>
      </div>
    </div>
  );
};

export default Movie;


```
</details>

<hr>

### <h3>AddMovie.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// addMovie.js
import { toast } from 'react-toastify';

const addMovie = async (title, newMovieRating, movies, setMovies, setTitle, setNewMovieRating, setSelectedSuggestion) => {
  try {
    // Check if any movie with the same title (case-insensitive) already exists in the list
    const isDuplicate = movies.some((movie) => movie.title.toLowerCase() === title.toLowerCase());
    if (isDuplicate) {
      toast.error('A movie with the same title already exists!');
      return;
    }

    const body = { title, rating: newMovieRating };
    const response = await fetch('http://localhost:5001/dashboard/movies', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        token: localStorage.token,
      },
      body: JSON.stringify(body),
    });
    const parseRes = await response.json();
    setMovies([...movies, parseRes]);
    setTitle('');
    setNewMovieRating(''); // Reset newMovieRating state
    setSelectedSuggestion(null);
    toast.success('Movie added successfully!');
  } catch (err) {
    console.error(err.message);
  }
};

export default addMovie;


```
</details>

<hr>

### <h3>DeleteMovie.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// deleteMovie.js
import { toast } from 'react-toastify';

const deleteMovie = async (id, movies, setMovies) => {
  try {
    await fetch(`http://localhost:5001/dashboard/movies/${id}`, {
      method: 'DELETE',
      headers: { token: localStorage.token },
    });
    setMovies(movies.filter((movie) => movie.movie_id !== id));
    toast.success('Movie deleted successfully!');
  } catch (err) {
    console.error(err.message);
  }
};

export default deleteMovie;


```
</details>

<hr>

### <h3>EditMovie.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// editMovie.js
import { toast } from 'react-toastify';

const editMovie = async (id, rating, movies, setMovies, setEditId) => {
  try {
    if (rating < 1 || rating > 5) {
      toast.error('Rating must be between 1 and 5.');
      return;
    }

    const body = { rating };
    await fetch(`http://localhost:5001/dashboard/movies/${id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        token: localStorage.token,
      },
      body: JSON.stringify(body),
    });
    const updatedMovies = movies.map((movie) =>
      movie.movie_id === id ? { ...movie, rating } : movie
    );
    setMovies(updatedMovies);
    setEditId(null);
    toast.success('Movie rating updated successfully!');
  } catch (err) {
    console.error(err.message);
  }
};

export default editMovie;

```
</details>

<hr>

### <h3>renderStars.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// renderStars.js

const renderStars = (rating) => {
    const filledStars = '★'.repeat(rating);
    const emptyStars = '☆'.repeat(5 - rating);
    return (
      <div>
        {filledStars}
        {emptyStars}
      </div>
    );
  };
  
  export default renderStars;
  
```
</details>

<hr>

### <h3>suggestions.js</h3>

<details>
<summary>Click to expand code</summary>

```js

// suggestions.js
import React from 'react';

export const getSuggestions = async (value, setSuggestions) => {
  try {
    const response = await fetch(`http://localhost:5001/dashboard/movies/suggestions?query=${value}`, {
      method: 'GET',
      headers: { token: localStorage.token },
    });
    const data = await response.json();
    setSuggestions(data.slice(0, 5)); // Limit to first 5 suggestions
  } catch (err) {
    console.error(err.message);
  }
};

export const onChange = (event, { newValue }, setTitle, setSelectedSuggestion) => {
  setTitle(newValue);
  // Reset selected suggestion when user types
  setSelectedSuggestion(null);
};

export const onSuggestionsFetchRequested = async ({ value }, setSuggestions) => {
  await getSuggestions(value, setSuggestions);
};

export const onSuggestionsClearRequested = (setSuggestions) => {
  setSuggestions([]);
};

export const renderSuggestion = suggestion => (
  <div className="suggestion-item">
    {suggestion}
  </div>
);

export const getSuggestionValue = suggestion => suggestion;

export const getSuggestionsContainer = ({ containerProps, children }) => {
  return (
    <div {...containerProps} className="suggestions-container">
      {children}
    </div>
  );
};

```
</details>

<hr>


