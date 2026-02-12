# OTHire Server Transfer Guide

This guide explains how to transfer your OTHire server from one computer to another.

## What You Need to Transfer

To successfully move your OTHire server to another computer, you need to transfer these components:

### 1. Essential Files and Directories

- **config.lua** - Main server configuration
- **data/** - All game data (scripts, maps, items, etc.)
- **MySql_Schema.sql** - Database schema (if starting fresh)
- **Server executable** - The compiled server binary for your target OS

### 2. Database

Your MySQL database contains all player data, accounts, houses, etc. You must export and import this.

## Step-by-Step Transfer Process

### Step 1: Backup Your Current Server

On your **source computer** (where the server currently runs):

1. **Stop the server** if it's running
2. **Export the database**:
   ```bash
   mysqldump -u root -p otserv > otserv_backup.sql
   ```
   Replace `root` with your MySQL username and `otserv` with your database name.

3. **Copy all required files** to a transfer location (USB drive, cloud storage, etc.):
   - config.lua
   - data/ (entire directory)
   - otserv_backup.sql (your database export)
   - Server executable (if same OS, otherwise you'll need to compile)

### Step 2: Prepare the Destination Computer

On your **destination computer** (where you want to run the server):

1. **Install MySQL Server**:
   - Windows: Download from https://dev.mysql.com/downloads/mysql/
   - Linux: `sudo apt-get install mysql-server` (Ubuntu/Debian)
   - macOS: `brew install mysql`

2. **Install required libraries** (if not compiling):
   - Lua 5.1
   - LibXML2
   - MySQL client libraries

3. **Compile the server** (if needed for your OS):
   ```bash
   cd source
   ./autogen.sh
   ./configure
   make
   ```

### Step 3: Transfer and Configure

1. **Copy all files** to your destination computer

2. **Import the database**:
   ```bash
   # Create the database
   mysql -u root -p -e "CREATE DATABASE otserv;"
   
   # Import your backup
   mysql -u root -p otserv < otserv_backup.sql
   ```

3. **Edit config.lua** for your new environment:

   ```lua
   -- Change the IP address
   -- For local testing, use:
   ip = "127.0.0.1"
   
   -- For hosting on a network/internet, use your computer's IP:
   -- ip = "YOUR.ACTUAL.IP.ADDRESS"
   
   -- Update database credentials if they changed
   sql_host = "127.0.0.1"
   sql_user = "root"
   sql_pass = "your_new_password"
   sql_db = "otserv"
   ```

4. **Update port forwarding** (if hosting online):
   - Forward ports 7171 and 7172 on your router
   - Point them to your computer's local IP

### Step 4: Test the Server

1. **Start the server**:
   ```bash
   ./othire  # Linux/Mac
   othire.exe  # Windows
   ```

2. **Check for errors** in the console output

3. **Test connection**:
   - Use Tibia 7.72 client
   - Connect to `127.0.0.1` (for local testing)
   - Login with default credentials: `123456` / `tibia`

## Important Configuration Settings

### IP Address Settings

The `config.lua` has several IP-related settings:

```lua
ip = "127.0.0.1"          -- Server listening address
login_port = "7171"        -- Login server port
game_port = "7172"         -- Game server port
status_port = "7171"       -- Status port
```

**Common scenarios:**

- **Local testing only**: Use `127.0.0.1`
- **LAN hosting**: Use your local network IP (e.g., `192.168.1.100`)
- **Internet hosting**: Use your public IP or domain name

### Database Settings

```lua
sql_type = "mysql"         -- Database type
sql_host = "127.0.0.1"     -- MySQL server address
sql_port = 3306            -- MySQL port
sql_user = "root"          -- Database username
sql_pass = ""              -- Database password
sql_db = "otserv"          -- Database name
```

## Troubleshooting

### Server won't start

**Problem**: "Failed to connect to database"
- **Solution**: Check your `sql_user`, `sql_pass`, and `sql_db` settings in config.lua
- Verify MySQL is running: `systemctl status mysql` (Linux) or check Services (Windows)

**Problem**: "Address already in use"
- **Solution**: Another program is using ports 7171/7172
- Change the ports in config.lua or stop the conflicting program

### Can't connect from other computers

**Problem**: Works locally but not from other computers
- **Solution**: 
  1. Change `ip = "127.0.0.1"` to your actual IP address
  2. Configure firewall to allow ports 7171 and 7172
  3. Set up port forwarding on your router (for internet access)

### Database import fails

**Problem**: Foreign key constraint errors
- **Solution**: Make sure you're using InnoDB storage engine:
  ```sql
  ALTER TABLE accounts ENGINE=InnoDB;
  ```

## Quick Reference: Files Needed

### Minimal Transfer (existing database)
```
config.lua
data/
otserv_backup.sql
othire (or othire.exe)
```

### Fresh Installation
```
config.lua
data/
MySql_Schema.sql (instead of backup)
othire (or othire.exe)
```

## Docker Alternative

If you want the easiest transfer method, use Docker:

```bash
# On source computer: commit your current setup
docker commit othire_container my-othire:latest
docker save my-othire:latest > othire-image.tar

# On destination computer: load and run
docker load < othire-image.tar
docker run -d -p 7171:7171 -p 7172:7172 my-othire:latest
```

This bundles everything (server, database, config) into a single portable image.

## Security Notes

- Change the default database password
- Don't expose your MySQL port (3306) to the internet
- Use strong passwords for accounts
- Keep backups of your database regularly

## Need Help?

- Check the [OTLand Forum](https://otland.net/threads/7-72-othire-0-0-3.246964/)
- Review the [GitHub Wiki](https://github.com/TwistedScorpio/OTHire/wiki)
- Open an issue on [GitHub](https://github.com/TwistedScorpio/OTHire/issues)

---

**Last Updated**: February 2026
