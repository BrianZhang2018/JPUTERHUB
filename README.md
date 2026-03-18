# JupyterHub Local Setup

Multi-user JupyterHub with DummyAuthenticator running on port 8088.

## Activate Virtual Environment

```bash
source jupyterhub_env/bin/activate
```

## Start JupyterHub

```bash
# Option 1: Use the startup script
./start_jupyterhub.sh

# Option 2: Manual start
source jupyterhub_env/bin/activate
jupyterhub -f jupyterhub_config.py
```

## Access JupyterHub

- URL: http://localhost:8088
- Username: any username (e.g. `user1`, `alice`, `testuser`)
- Password: `test`

## Default Interface

JupyterLab launches by default. Classic Notebook is also available.

## Stop JupyterHub

Press `Ctrl+C` in the terminal where JupyterHub is running.

## File Structure

```
jputerhub/
├── jupyterhub_env/        # Virtual environment
├── runtime/               # Runtime files (DB, cookie secret)
├── jupyterhub_config.py   # Configuration
├── start_jupyterhub.sh    # Startup script
└── README.md              # This file
```

## Python Kernel

Python 3.11 kernel is available in JupyterLab via ipykernel.
