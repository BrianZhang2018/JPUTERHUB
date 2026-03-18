# JupyterHub Future Implementation TODO

## 1. Multi-User Isolation
> Currently all users are mapped to the `brianzhang` OS user via `pre_spawn_hook`. True user isolation is not yet implemented.

- [ ] **Option A: Per-user venv hook** — spawn all users as `brianzhang` OS user but give each their own isolated Python virtual environment and home subdirectory
- [ ] **Option B: Real macOS system accounts** — create actual macOS users (`user1`, `user2`, etc.) via System Preferences and remove the `pre_spawn_hook` workaround
- [ ] **Option C: DockerSpawner** — each user spawns inside their own Docker container (no OS user required, full filesystem isolation)

---

## 2. Environment Management
> Currently only one Python 3.11 kernel is available to all users.

- [ ] Add **named kernels** for different project types (e.g. `data-science`, `ml`, `web-scraping`) using separate venvs registered via `ipykernel install`
- [ ] Add **per-user venv** support — automatically create and activate a personal venv per user at spawn time via `pre_spawn_hook`
- [ ] Explore **nb_conda_kernels** — auto-discover all conda environments as selectable kernels in JupyterLab
- [ ] Install common data science packages: `pandas`, `numpy`, `matplotlib`, `scikit-learn`
- [ ] Install ML packages: `torch`, `tensorflow`, `transformers`

---

## 3. Authentication
> Currently using DummyAuthenticator (any username, password: `test`). Not suitable for real use.

- [ ] Replace DummyAuthenticator with **PAMAuthenticator** (real macOS system users)
- [ ] Or configure **NativeAuthenticator** — lets users self-register with username/password stored in the JupyterHub DB
- [ ] Or configure **OAuthenticator** — login via GitHub, Google, etc.
- [ ] Set up **admin users** (`c.Authenticator.admin_users`) to manage other users
- [ ] Set up **allowed users** whitelist (`c.Authenticator.allowed_users`)

---

## 4. Security
> Currently running without SSL, no resource limits, no user restrictions.

- [ ] Enable **SSL/HTTPS** — generate self-signed cert or use Let's Encrypt
- [ ] Set `c.JupyterHub.bind_url` to use HTTPS
- [ ] Add **resource limits** per user (CPU, RAM) via `c.Spawner.mem_limit` and `c.Spawner.cpu_limit`
- [ ] Restrict which users can log in (`c.Authenticator.allowed_users`)
- [ ] Set up **idle server culling** — automatically shut down inactive user servers to save resources

---

## 5. Spawner Upgrade
> Currently using LocalProcessSpawner (default). Limited isolation and scalability.

- [ ] Evaluate **DockerSpawner** — full container isolation per user
- [ ] Evaluate **KubeSpawner** — Kubernetes-based spawning (for future scaling)
- [ ] Configure `c.Spawner.mem_limit` and `c.Spawner.cpu_limit` for fair resource sharing

---

## 6. Persistence & Storage
> Currently users all share `/Users/brianzhang/` as their notebook directory.

- [ ] Create **per-user notebook directories** (e.g. `/Users/brianzhang/ai/jputerhub/users/{username}/`)
- [ ] Configure `c.Spawner.notebook_dir` to point to per-user directories
- [ ] Set up automatic **directory creation** for new users at first login

---

## 7. Operational Improvements
> JupyterHub is currently started manually as a background process.

- [ ] Create a **launchd plist** to run JupyterHub as a macOS service (auto-start on boot)
- [ ] Set up **log rotation** for `runtime/jupyterhub.log`
- [ ] Configure **idle culler** extension (`jupyterhub-idle-culler`) to stop inactive servers
- [ ] Add a **stop script** (`stop_jupyterhub.sh`) to gracefully shut down JupyterHub
- [ ] Update `start_jupyterhub.sh` to save PID to a file for easier process management

---

## 8. UI & UX
- [ ] Set a custom **JupyterHub logo and theme**
- [ ] Configure **announcement banners** for users
- [ ] Enable **collaborative mode** (real-time notebook collaboration via `jupyter-collaboration`)

---

## Priority Order (Suggested)

| Priority | Item |
|---|---|
| High | Multi-user isolation (Section 1) |
| High | Authentication upgrade (Section 3) |
| High | Per-user notebook directories (Section 6) |
| Medium | Environment management (Section 2) |
| Medium | Security / SSL (Section 4) |
| Medium | Operational improvements (Section 7) |
| Low | Spawner upgrade to Docker (Section 5) |
| Low | UI & UX (Section 8) |
