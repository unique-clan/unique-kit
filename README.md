## How to install

A Linux server, preferably Debian or Ubuntu, with a static IPv4 address is required. Start by creating a new user (typically `urace`) with a home directory (typically `/home/urace`) and preferably Bash as login shell. Then enable lingering for the user:

```
loginctl enable-linger urace
```

Install build dependencies for the game:

```
apt install git cmake ninja-build g++ cargo libsqlite3-dev libpng-dev libcurl4-openssl-dev libmariadb-dev
```

Add our SSH public keys to the user's `.ssh/authorized_keys` and make sure the file permissions are correct. We need access for administrative tasks such as updating the servers and looking at game logs.

The rest of this guide does not require root and can either be performed by the server owner or by us.

Clone the following repositories into the user's home directory (ask us for an access token):

```
git clone https://github.com/unique-clan/unique-kit.git
git clone https://github.com/unique-clan/unique-maps.git
git clone https://github.com/unique-clan/unique-race.git
git clone https://uger:token@github.com/unique-clan/unique-secrets.git
```

Compile the game server:

```
./unique-kit/update-game
```

Create `autoexec_local.cfg` for your location (ask us for database credentials):

```
sv_name Unique Race GER

sv_use_sql 1
sv_sql_servername GER
add_sqlserver r urace record uger password 5.75.153.72 44936 0
add_sqlserver w urace record uger password 5.75.153.72 44936 0
```

Start and enable the user units (you can choose the port range freely):

```
systemctl --user link unique-kit/update-maps.service
systemctl --user enable --now unique-kit/update-maps.timer
systemctl --user link unique-kit/update-secrets.service
systemctl --user enable --now unique-kit/update-secrets.timer
systemctl --user link unique-kit/restart-nightly.service
systemctl --user enable --now unique-kit/restart-nightly.timer
systemctl --user link unique-kit/game-server@.service
systemctl --user enable --now game-server@{8300..8303}.service
```
