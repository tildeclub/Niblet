````markdown
# Niblet
Event-driven, modular IRC bot for Python 3.

---

## Quick start (Linux/macOS)

### 0) System prerequisites
- Python **3.11+** (tested on **3.13**)
- Git
- Build tools only if wheels aren’t available:
  - **Fedora/RHEL**: `sudo dnf install -y gcc python3-devel libxml2-devel libxslt-devel pkgconf-pkg-config`
  - **Debian/Ubuntu**: `sudo apt-get install -y build-essential python3-dev libxml2-dev libxslt1-dev pkg-config`

### 1) Get the code
```bash
git clone https://github.com/niblet-irc/niblet.git
cd niblet
````

### 2) Create a virtualenv

**bash/zsh**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

**fish**

```fish
python3 -m venv .venv
source .venv/bin/activate.fish
```

### 3) Install dependencies

`requirements.txt` targets modern Python (includes `lxml>=6,<7`, `requests-toolbelt>=1.0.0`, and `legacy-cgi` for Py3.13 compatibility).

```bash
pip install -U pip wheel
pip install -r requirements.txt
# If pip compiles lxml from source, install the -devel headers from step 0.
```

### 4) Configure

```bash
mkdir -p ~/.niblet
cp docs/bot.conf.example ~/.niblet/bot.conf
# Edit ~/.niblet/bot.conf: set nick/ident/realname, servers/channels, and enable modules.
# Some modules (google/youtube/spotify/…​) require API keys; add them here before enabling.
# The 'user_time' module depends on the 'location' module being configured.
```

### 5) First run

```bash
./nibletd -a   # add your network(s)
./nibletd      # start the bot (foreground)
```

### 6) Master admin password

```bash
./nibletctl command master-password
# Use this in IRC to register your admin account.
```

---

## Run as a service (optional)

```ini
# /etc/systemd/system/niblet.service
[Unit]
Description=Niblet IRC bot
After=network-online.target
Wants=network-online.target

[Service]
User=niblet
WorkingDirectory=/opt/niblet
Environment=PATH=/opt/niblet/.venv/bin
ExecStart=/opt/niblet/nibletd
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Deploy:

```bash
sudo useradd -r -s /usr/sbin/nologin niblet 2>/dev/null || true
sudo cp -r ~/niblet /opt/niblet && sudo chown -R niblet:niblet /opt/niblet
cd /opt/niblet
sudo -u niblet python3 -m venv .venv
sudo -u niblet .venv/bin/pip install -U pip wheel
sudo -u niblet .venv/bin/pip install --only-binary=:all: -r requirements.txt
sudo systemctl daemon-reload
sudo systemctl enable --now niblet
```

**Upgrade:**

```bash
cd /opt/niblet
sudo -u niblet git pull
sudo -u niblet .venv/bin/pip install -U -r requirements.txt
sudo systemctl restart niblet
```

---

## Backups

Back up the entire `~/.niblet` directory (config, database, rotated logs). Tools like
[borgbackup](https://borgbackup.readthedocs.io/en/stable/) work well.

---

## Docs, Support, License

* Configuration help: see `docs/help/config.md`.
* Chat: `#niblet` on irc.libera.chat.
* License: GNU GPL v2.0 — see [LICENSE](LICENSE).

