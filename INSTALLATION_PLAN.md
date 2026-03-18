# JupyterHub Installation Plan

## Overview
Install JupyterHub in a virtual environment with multi-user support, dummy authenticator, JupyterLab/Jupyter Notebook, and Python 3.11 kernel on port 8088.

## Environment Specifications
- **Location**: `/Users/brianzhang/ai/jputerhub`
- **Python Version**: 3.11.13
- **Virtual Environment Name**: `jupyterhub_env`
- **Port**: 8088
- **Authenticator**: DummyAuthenticator (for testing)
- **Spawner**: Default LocalProcessSpawner
- **Notebook Interface**: JupyterLab and Jupyter Notebook

## Detailed Implementation Steps

### Phase 1: Virtual Environment Setup

**Step 1.1**: Create virtual environment
- Command: `python3 -m venv jupyterhub_env`
- Location: `/Users/brianzhang/ai/jputerhub/jupyterhub_env`

**Step 1.2**: Activate virtual environment
- Command: `source jupyterhub_env/bin/activate`

**Step 1.3**: Upgrade pip, setuptools, and wheel
- Command: `pip install --upgrade pip setuptools wheel`

### Phase 2: Install configurable-http-proxy

**Step 2.1**: Install configurable-http-proxy globally via npm
- Command: `npm install -g configurable-http-proxy`
- Note: This is a Node.js package required by JupyterHub for routing

**Step 2.2**: Verify configurable-http-proxy installation
- Command: `configurable-http-proxy --version`

### Phase 3: Install JupyterHub and Dependencies

**Step 3.1**: Install JupyterHub
- Command: `pip install jupyterhub`

**Step 3.2**: Install JupyterLab
- Command: `pip install jupyterlab`

**Step 3.3**: Install Jupyter Notebook (classic)
- Command: `pip install notebook`

**Step 3.4**: Install ipykernel for Python 3.11 kernel
- Command: `pip install ipykernel`

**Step 3.5**: Verify installations
- Commands:
  - `jupyterhub --version`
  - `jupyter --version`
  - `jupyter lab --version`

### Phase 4: Configure JupyterHub

**Step 4.1**: Generate default JupyterHub configuration file
- Command: `jupyterhub --generate-config`
- Output file: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`

**Step 4.2**: Edit jupyterhub_config.py to set port 8088
- File: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`
- Find line: `# c.JupyterHub.bind_url = 'http://:8000'`
- Replace with: `c.JupyterHub.bind_url = 'http://:8088'`

**Step 4.3**: Configure DummyAuthenticator
- File: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`
- Find line: `# c.JupyterHub.authenticator_class = 'jupyterhub.auth.PAMAuthenticator'`
- Replace with: `c.JupyterHub.authenticator_class = 'jupyterhub.auth.DummyAuthenticator'`

**Step 4.4**: Set default password for DummyAuthenticator (optional but recommended)
- File: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`
- Add new line: `c.DummyAuthenticator.password = 'test'`
- Note: Any username with password 'test' will be able to login

**Step 4.5**: Configure default URL to JupyterLab
- File: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`
- Find line: `# c.Spawner.default_url = ''`
- Replace with: `c.Spawner.default_url = '/lab'`
- Note: This makes JupyterLab the default interface instead of classic notebook

**Step 4.6**: Set the notebook directory
- File: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`
- Add new line: `c.Spawner.notebook_dir = '~/'`
- Note: Users will start in their home directory

### Phase 5: Create Directory Structure

**Step 5.1**: Create directory for JupyterHub runtime files
- Command: `mkdir -p /Users/brianzhang/ai/jputerhub/runtime`

**Step 5.2**: Configure JupyterHub to use runtime directory
- File: `/Users/brianzhang/ai/jputerhub/jupyterhub_config.py`
- Add new line: `c.JupyterHub.cookie_secret_file = '/Users/brianzhang/ai/jputerhub/runtime/jupyterhub_cookie_secret'`
- Add new line: `c.JupyterHub.db_url = 'sqlite:////Users/brianzhang/ai/jputerhub/runtime/jupyterhub.sqlite'`

### Phase 6: Testing and Verification

**Step 6.1**: Start JupyterHub
- Command: `jupyterhub -f jupyterhub_config.py`
- Working directory: `/Users/brianzhang/ai/jputerhub`

**Step 6.2**: Verify JupyterHub is running
- Check console output for: "JupyterHub is now running at http://:8088/"

**Step 6.3**: Test login via browser
- URL: `http://localhost:8088`
- Username: any username (e.g., "user1", "testuser")
- Password: `test`

**Step 6.4**: Verify JupyterLab launches
- After login, confirm JupyterLab interface appears

**Step 6.5**: Verify Python 3.11 kernel is available
- In JupyterLab, create new notebook
- Check that Python 3.11 kernel is available in launcher

**Step 6.6**: Test multi-user functionality
- Open incognito/private browser window
- Login with different username
- Verify separate session is created

### Phase 7: Documentation

**Step 7.1**: Create README.md with usage instructions
- File: `/Users/brianzhang/ai/jputerhub/README.md`
- Contents:
  - How to activate virtual environment
  - How to start JupyterHub
  - How to access JupyterHub (URL and credentials)
  - How to stop JupyterHub
  - How to add more users

**Step 7.2**: Create startup script for convenience
- File: `/Users/brianzhang/ai/jputerhub/start_jupyterhub.sh`
- Contents:
  ```bash
  #!/bin/bash
  cd /Users/brianzhang/ai/jputerhub
  source jupyterhub_env/bin/activate
  jupyterhub -f jupyterhub_config.py
  ```
- Make executable: `chmod +x start_jupyterhub.sh`

## Expected File Structure After Installation

```
/Users/brianzhang/ai/jputerhub/
├── jupyterhub_env/           # Virtual environment
├── runtime/                  # JupyterHub runtime files
│   ├── jupyterhub.sqlite    # User database
│   └── jupyterhub_cookie_secret
├── jupyterhub_config.py      # Configuration file
├── start_jupyterhub.sh       # Startup script
└── README.md                 # Usage documentation
```

## Configuration Summary

**jupyterhub_config.py key settings:**
- `c.JupyterHub.bind_url = 'http://:8088'` - Port configuration
- `c.JupyterHub.authenticator_class = 'jupyterhub.auth.DummyAuthenticator'` - Dummy auth
- `c.DummyAuthenticator.password = 'test'` - Password for all users
- `c.Spawner.default_url = '/lab'` - Default to JupyterLab
- `c.Spawner.notebook_dir = '~/'` - User home directory
- `c.JupyterHub.cookie_secret_file = '/Users/brianzhang/ai/jputerhub/runtime/jupyterhub_cookie_secret'`
- `c.JupyterHub.db_url = 'sqlite:////Users/brianzhang/ai/jputerhub/runtime/jupyterhub.sqlite'`

---

# IMPLEMENTATION CHECKLIST:

1. Create virtual environment: `python3 -m venv jupyterhub_env`
2. Activate virtual environment: `source jupyterhub_env/bin/activate`
3. Upgrade pip, setuptools, and wheel: `pip install --upgrade pip setuptools wheel`
4. Install configurable-http-proxy globally: `npm install -g configurable-http-proxy`
5. Verify configurable-http-proxy installation: `configurable-http-proxy --version`
6. Install JupyterHub: `pip install jupyterhub`
7. Install JupyterLab: `pip install jupyterlab`
8. Install Jupyter Notebook: `pip install notebook`
9. Install ipykernel: `pip install ipykernel`
10. Verify JupyterHub installation: `jupyterhub --version`
11. Verify Jupyter installation: `jupyter --version`
12. Generate JupyterHub configuration: `jupyterhub --generate-config`
13. Edit jupyterhub_config.py - set bind_url to 'http://:8088'
14. Edit jupyterhub_config.py - set authenticator_class to 'jupyterhub.auth.DummyAuthenticator'
15. Edit jupyterhub_config.py - set DummyAuthenticator.password to 'test'
16. Edit jupyterhub_config.py - set Spawner.default_url to '/lab'
17. Edit jupyterhub_config.py - set Spawner.notebook_dir to '~/'
18. Create runtime directory: `mkdir -p runtime`
19. Edit jupyterhub_config.py - set cookie_secret_file to '/Users/brianzhang/ai/jputerhub/runtime/jupyterhub_cookie_secret'
20. Edit jupyterhub_config.py - set db_url to 'sqlite:////Users/brianzhang/ai/jputerhub/runtime/jupyterhub.sqlite'
21. Start JupyterHub: `jupyterhub -f jupyterhub_config.py`
22. Verify JupyterHub is running on port 8088
23. Test login at http://localhost:8088 with any username and password 'test'
24. Verify JupyterLab interface launches
25. Verify Python 3.11 kernel is available in JupyterLab
26. Test multi-user functionality with different username in incognito window
27. Stop JupyterHub (Ctrl+C)
28. Create README.md with usage instructions
29. Create start_jupyterhub.sh startup script
30. Make start_jupyterhub.sh executable: `chmod +x start_jupyterhub.sh`
