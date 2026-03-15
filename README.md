# jupyter-srv

Minimal systemd-managed JupyterLab server installed under `/srv`.

The package creates a dedicated system user, isolated directory layout, and a Python virtual environment containing JupyterLab.  
The server is intended to run as a persistent service on a Linux host.

The implementation relies only on:

- `systemd`
- `python`
- a local virtual environment in `/srv/jupyter-srv/venv`

No global Python packages are required.

---

# Features

- dedicated system user (`jupytersrv`)
- isolated runtime directories under `/srv/jupyter-srv`
- automatic virtualenv creation
- automatic installation of `jupyterlab`
- systemd service with security hardening
- configurable IP/port/token via environment file
- helper script for password setup

---

# Directory layout

The service uses the following structure:

```
/srv/jupyter-srv
├── venv
├── notebooks
├── config
├── data
└── runtime
```

These directories are created via `systemd-tmpfiles`.

Permissions:

```
0770
owner: jupytersrv
group: jupytersrv
```

Human users must be added to the `jupytersrv` group to access the notebooks.

---

# Service behavior

The service runs:

```
/srv/jupyter-srv/venv/bin/jupyter-lab
```

Working directory:

```
/srv/jupyter-srv/notebooks
```

Configured through:

```
/etc/jupyter-srv.conf
```

Default configuration:

```
JUPYTER_IP=0.0.0.0
JUPYTER_PORT=8080
JUPYTER_TOKEN_ARG=
JUPYTER_EXTRA_ARGS=
```

Token authentication is enabled by default unless explicitly disabled.

---

# Installation (Arch Linux)

Build and install with:

```
makepkg -si
```

After installation:

```
sudo install -Dm644 /usr/share/jupyter-srv/jupyter-srv.conf.example /etc/jupyter-srv.conf
```

Grant access to a user:

```
sudo usermod -aG jupytersrv YOUR_LOGIN
```

Re-login to apply group membership.

Enable the service:

```
sudo systemctl enable --now jupyter-srv.service
```

---

# Setting a password

The package provides a helper command:

```
jupyter-srv-set-password
```

The script executes:

```
sudo -u jupytersrv -H env JUPYTER_CONFIG_DIR=/srv/jupyter-srv/config /srv/jupyter-srv/venv/bin/python -m jupyter_server.auth password
```

The password hash is stored in:

```
/srv/jupyter-srv/config/jupyter_server_config.json
```

After setting the password the service is restarted automatically.

---

# Service configuration

The systemd unit:

```
/usr/lib/systemd/system/jupyter-srv.service
```

Important settings:

```
User=jupytersrv
Group=jupytersrv
WorkingDirectory=/srv/jupyter-srv/notebooks
```

Security options enabled:

```
NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
ReadWritePaths=/srv/jupyter-srv
```

---

# Removing the package

Removing the package does **not delete the data directory**.

The uninstall message states:

```
/srv/jupyter-srv was left intact on purpose.
Remove it manually if you really want:
sudo rm -rf /srv/jupyter-srv
```

---

# Purpose

This package provides a reproducible way to run JupyterLab as a system service with:

- clean separation from user environments
- predictable filesystem layout
- systemd lifecycle management

It is intended for small servers, development machines, or internal notebook services.
