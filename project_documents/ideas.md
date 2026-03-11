# Project Outline Idea

## Goal and Parameters
- Create movie recommendations based on cosine similarity
- Data: The TMDB used as a base (see: https://www.themoviedb.org/)
- Input: List of user chosen movies
- Output: List of recommended or similar movies

## General
- Data stored in RDBMS
- Prediction model:
    - Create custom dense embeddings for the movie
    - Predictions: Create embeddings for each input movie, average them, calculate cosine similarity
- Prediction available over an API and over a GUI
- GUI uses the API endpoint

## Tooling
| Tool | Purpose |
|------|---------|
| GitHub | Version control and deployment pipeline |
| PostgreSQL | Database |
| scikit-learn | Encoding data |
| PyTorch | Create embeddings |
| Streamlit | Web GUI and processing user inputs |
| FastAPI | RESTful interface for requesting recommendations and using Streamlit connection |
| Docker | Containerization (one container for Streamlit and Backend and FastAPI) |
| ONNX | Save and use model |
| Some VPS Hoster | Host all the containers (and DB?) |
| Supabase | Hosting the DB? |

## Application "Architecture" (on VPS or local)
- DB Server on VPS or Supabase
- Container backend:
    - Pretrained ONNX Model file
    - ORM Connection to DB
    - FastAPI Interface
- Container frontend:
    - Streamlit app
    - Connecting to the FastAPI Service over web

## Development Workflow
- tbd

## Project structure (vibe written)
Note: The PostgreSQL instance has to be set up independently.

```
your-repo/
├── .github/
│   └── workflows/
│       ├── deploy.yml          # main pipeline
│       └── train.yml           # optional: retrain model
├── backend/
│   ├── Dockerfile
│   ├── main.py                 # FastAPI
│   ├── model/
│   │   └── movie_encoder.onnx
│   └── requirements.txt
├── frontend/
│   ├── Dockerfile
│   └── app.py                  # Streamlit
├── training/
│   └── train.py                # PyTorch training script
└── docker-compose.yml
```