# Server Transfer Checklist

Use this checklist when transferring your OTHire server to another computer.

## On Source Computer (Current Server)

- [ ] Stop the server
- [ ] Export the database:
  ```bash
  mysqldump -u root -p otserv > otserv_backup.sql
  ```
- [ ] Copy these files/folders to transfer media:
  - [ ] config.lua
  - [ ] data/ (entire directory)
  - [ ] otserv_backup.sql
  - [ ] server executable (if same OS)

## On Destination Computer (New Server)

- [ ] Install MySQL Server
- [ ] Install required libraries (Lua 5.1, LibXML2, MySQL client)
- [ ] Copy all transferred files to new location
- [ ] Create database and import data:
  ```bash
  mysql -u root -p -e "CREATE DATABASE otserv;"
  mysql -u root -p otserv < otserv_backup.sql
  ```
- [ ] Edit config.lua:
  - [ ] Update `ip` setting (line 29)
  - [ ] Update `sql_host` if needed (line 344)
  - [ ] Update `sql_user` if needed (line 346)
  - [ ] Update `sql_pass` if needed (line 347)
  - [ ] Update `sql_db` if needed (line 343)
- [ ] Configure firewall (allow ports 7171 and 7172)
- [ ] Set up port forwarding (if hosting online)
- [ ] Test: Start server and check for errors
- [ ] Test: Connect with Tibia client

## Common IP Settings

Choose based on your needs:

- [ ] **Local testing only**: `ip = "127.0.0.1"`
- [ ] **LAN hosting**: `ip = "192.168.x.x"` (your local IP)
- [ ] **Internet hosting**: `ip = "0.0.0.0"` or your public IP

## Files You DON'T Need to Transfer

These are generated or can be recreated:
- *.log files
- Temporary files
- Compiled object files (.o, .obj)
- IDE project files

## Quick Test Commands

```bash
# Check MySQL is running
systemctl status mysql        # Linux
# or check Services in Windows

# Test database connection
mysql -u root -p otserv -e "SHOW TABLES;"

# Check ports are available
netstat -an | grep 7171
netstat -an | grep 7172
```

## If Something Goes Wrong

1. Check server console for error messages
2. Verify database credentials in config.lua
3. Ensure MySQL is running
4. Check firewall isn't blocking ports
5. See full [TRANSFER_GUIDE.md](TRANSFER_GUIDE.md) for troubleshooting

---

**Need help?** See the complete [TRANSFER_GUIDE.md](TRANSFER_GUIDE.md) for detailed instructions.
