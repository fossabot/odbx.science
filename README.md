# odbx.science

This repo contains some notes about the server running at odbx.science. 

## Hardware 

As of 02/12/19, the server runs as an HPCS OpenStack VM (quadcore Haswell, 8 GB RAM), with three virutal disks attached:

- `/dev/vda`, 80 GB, mounted at `/` contains OS and apps
- `/dev/vdb`, 1 TB, mounted at `/data/sql`, contains SQL database for OQMD
- `/dev/vdc`, 500 GB, mounted at `/data/mongo`, contains our MongoDB.

## Services & security

- `sshd` with password authentication and root login disabled, on port 22.
- `mongod` runs on the default port (which is closed) with authentication disabled, runs as an unpriveleged user and hosts the `crystals` database and various collections.
- `uvicorn`, the web server, runs as an unpriveleged user on port 5000. `iptables` forwards all port 80 traffic to port 5000, but must be reenabled at startup.
- `fail2ban` adds an extra layer of protection to `ssh` by banning password scrapers after a few attempts.
- `mysql` can be enabled to update the OQMD copy, but is not on by default.

## Software

- Currently the VM hosts a nearly default [MaterialsConsortia/optimade-python-tools](https://github.com/Materials-Consortia/optimade-python-tools) server that points to a MongoDB created by the converter in [ml-evs/matador_optimade](https://github.com/ml-evs/matador_optimade).
- JSON responses are served by the `uvicorn` web server, which is using FastAPI, Starlette and pydantic under the hood.

## Domains & SSL

- The domain `odbx.science` was purchased until 2023 from Namecheap, which currently redirects to the public IP of the VM.
- All `*@odbx.science` email addresses currently redirect to my personal account.
- The domain came with a 1 year SSL certificate (~£4 year after), but free alternatives exists (consider e.g. `cert-bot`).
