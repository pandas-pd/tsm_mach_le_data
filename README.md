# MSE TSM MachLeData Group Project

## About the Project
Repo for the Groupproject TSM MachLeData.<br>
Project members:
- Hemanthi Naidu
- Pedro Stark
- ...
- Joël Tauss

Goal:
- Building a movie reccomendation system with sklearn and pytorch
- Creating a deployment pipeline
- Building and hosting the application

See project_documents for furher information

## Tooling and software requirements
| Software / Tool | Version | Link                                            |
|-----------------|---------|-------------------------------------------------|
| Python          | 3.12    | https://www.python.org/downloads/               |
| PostgresQL      | 18      | https://www.postgresql.org/download/            |
| Docker Desktop  | 4.19    | https://docs.docker.com/get-started/get-docker/ |
| ...             | ...     | ...                                             |


## Getting Started

### Setting up the python environment

1. Make sure uv is installed
https://docs.astral.sh/uv/getting-started/installation/

2. Creating a virtual environment
```sh
uv venv .venv --python=3.12.4
```

3. Activating the virtual environment

On macOS/Linux:
```sh
source .venv/bin/activate
```

On Windows (PowerShell):
```sh
.venv\Scripts\Activate
```

4. Installing the dependencies

```sh
uv pip install -r requirements.txt
```