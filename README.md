# Go Movies CRUD API

This project implements a simple CRUD (Create, Read, Update, Delete) API for managing movies using Go. It uses the Gorilla Mux router for handling HTTP routes and serves JSON responses to interact with the movie data.

## Project Structure
    . 
    ├── go-movies-crud          # Compiled binary of the server 
    ├── main.go                 # Go source code for the Movies CRUD API 
    ├── go.mod                  # Module file to track dependencies 
    └── go.sum                  # Dependency checksum file

## Requirements

To run this project, you need the following:
- **Go** (version 1.23.2 or later)
- A tool like Postman or `curl` to test API requests.

## How to Run the Project

### Option 1: Run from Source

1. **Clone the project files** into a directory on your machine.
2. **Navigate to the project directory** in your terminal:

```bash
cd /path/to/project
```
**Install dependencies**: Run the following command to install the required dependencies:
```bash
go mod tidy
```
**Run the server**: To run the Go server directly from the source code:
```bash
go run main.go
```
**Alternatively, to compile the binary**:
```bash
go build -o go-movies-crud
./go-movies-crud
```

**Access the API**: The server will start at port ``3000``. You can interact with the API using HTTP requests to ``http://localhost:3000``.

### Option 2: Run the Compiled Binary
If you already have the ``go-movies-crud`` binary:

- Open a terminal and navigate to the directory containing go-movies-crud.
- Run the binary:
```bash
./go-movies-crud
```
## API Endpoints
The following are the available API endpoints for managing movies:

### 1. Get All Movies

**Endpoint**: ``/movies``

**Method**: GET

**Description**: Returns a list of all movies.

**Example**:
```bash
curl http://localhost:3000/movies
```
### 2. Get Movie by ID

**Endpoint**: ``/movies/{id}``

**Method**: GET

**Description**: Returns a single movie based on the movie ID.

**Example**:
```bash
curl http://localhost:3000/movies/1
```
### 3. Create a New Movie

**Endpoint**: ``/movies``

**Method**: POST

**Description**: Creates a new movie by sending JSON data in the request body.

**Example**:
```bash
curl -X POST http://localhost:3000/movies \
  -H "Content-Type: application/json" \
  -d '{"isbn":"987654", "title":"New Movie", "director":{"firstname":"John", "lastname":"Doe"}}'
```
### 4. Update an Existing Movie

**Endpoint**: ``/movies/{id}``

**Method**: PUT

**Description**: Updates a movie based on its ID by sending JSON data in the request body.

**Example**:
```bash
curl -X PUT http://localhost:3000/movies/1 \
  -H "Content-Type: application/json" \
  -d '{"isbn":"987654", "title":"Updated Movie", "director":{"firstname":"Jane", "lastname":"Doe"}}'
```
### 5. Delete a Movie

**Endpoint**: ``/movies/{id}``

**Method**: DELETE

**Description**: Deletes a movie based on its ID.

**Example**:
```bash
curl -X DELETE http://localhost:3000/movies/1
```
## Project Overview
This project includes the following core functionalities:

- **CRUD operations for movies**: The server allows creating, reading, updating, and deleting movies through JSON API requests.

- **Gorilla Mux for routing**: The Gorilla Mux package is used to handle the API routes.
- **In-memory data store**: The movies are stored in an in-memory slice, which is cleared when the server stops.

### main.go
The ``main.go`` file contains all of the server logic, including routing, data handling, and CRUD operations.

- **Movie struct**: Represents a movie with fields for ID, ISBN, Title, and Director.
```go
type Movie struct {
    ID       string    `json:"id"`
    Isbn     string    `json:"isbn"`
    Title    string    `json:"title"`
    Director *Director `json:"director"`
}
```
- **Director struct**: Represents the director of the movie with fields for first and last name.
```go
type Director struct {
    Firstname string `json:"firstname"`
    Lastname  string `json:"lastname"`
}
```
- **getMovies**: Handles GET requests to return all movies.
```go
func getMovies(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(movies)
}
```
- **getMovie**: Handles GET requests for retrieving a single movie by its ID.
```go
func getMovie(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-type", "application/json")
    params := mux.Vars(r)
    for _, item := range movies {
        if item.ID == params["id"] {
            json.NewEncoder(w).Encode(item)
            return
        }
    }
}
```
- **createMovie**: Handles POST requests to create a new movie. A random ID is generated for each new movie.
```go
func createMovie(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-type", "application/json")
    var movie Movie
    _ = json.NewDecoder(r.Body).Decode(&movie)
    movie.ID = strconv.Itoa(rand.Intn(100000000))
    movies = append(movies, movie)
    json.NewEncoder(w).Encode(movie)
}
```
- **updateMovie**: Handles PUT requests to update an existing movie based on its ID.
```go
func updateMovie(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-type", "application/json")
    params := mux.Vars(r)
    for index, item := range movies {
        if item.ID == params["id"] {
            movies = append(movies[:index], movies[index+1:]...)
            var movie Movie
            _ = json.NewDecoder(r.Body).Decode(&movie)
            movie.ID = params["id"]
            movies = append(movies, movie)
            json.NewEncoder(w).Encode(movie)
            return
        }
    }
}
```
- **deleteMovie**: Handles DELETE requests to delete a movie by its ID.
```go
func deleteMovie(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    params := mux.Vars(r)
    for index, item := range movies {
        if item.ID == params["id"] {
            movies = append(movies[:index], movies[index+1:]...)
            break
        }
    }
    json.NewEncoder(w).Encode(movies)
}
```
- **Routing**: The ``mux.Router`` is used to handle all the routes for the API.
```go
r := mux.NewRouter()
r.HandleFunc("/movies", getMovies).Methods("GET")
r.HandleFunc("/movies/{id}", getMovie).Methods("GET")
r.HandleFunc("/movies", createMovie).Methods("POST")
r.HandleFunc("/movies/{id}", updateMovie).Methods("PUT")
r.HandleFunc("/movies/{id}", deleteMovie).Methods("DELETE")
```
- **Starting the server**: The server listens on port ``3000`` and logs any fatal errors.
```go
fmt.Printf("Starting server at port 3000\n")
log.Fatal(http.ListenAndServe(":3000", r))
```
## Extending the Project
You can extend this project by:

- Adding validation to the movie data before saving it.

- Persisting the movies in a database instead of using an in-memory slice.

- Adding more endpoints for advanced querying or searching movies.
## Conclusion
This project provides a basic CRUD API for managing movies using Go. It demonstrates how to handle routes, perform CRUD operations, and work with JSON data.

