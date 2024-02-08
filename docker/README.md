# Startup 

To launch the full multi-container solution, please run:

```bash
docker compose up
```

- To force a new build of the containers,  add a `--build` flag at the end of the previous command.
- To run it in background, please add a `-d` flag at the end of the previous command.
- For more options, check `docker compose up --help`

To see services' output, you can run:

```bash
docker compose logs [OPTIONS] [SERVICE...]
```

- For more options, check `docker compose logs --help`

To stop it:

```bash
docker compose down
```

- If you also want to remove all internal data except the setup, you can add `--volumes` flag at the end of the previous command.
- For more options, check `docker compose down --help`

If you're not familiarized with `docker compose` tool, it will be recommended to check its [website](https://docs.docker.com/compose/) and `docker compose --help` output.

## Auto startup

- update `docker-compose.yml` file location in [mi.service](./mi.service)
- setup file service:

```bash 
sudo cp mi.service /etc/systemd/system/mi.service
sudo chmod +x /etc/systemd/system/mi.service
sudo systemctl enable mi
```

- then you can use `sudo systemctl [start|status|stop] mi`, for example:
   -  to start: 
```
sudo systemctl start mi
```

   -  to stop: 
```
sudo systemctl stop mi
```

## Run some services

Instead of running all services provided, a partial run is allowed by doing:

```bash
docker compose -f docker/docker-compose.yml up <service_to_run>
```

Main options are:

- `gnb`: It will start the srsRAN Project gNB + Open5G core, without UI stack.
- `grafana`: It will start the full Grafana + InfluxDB + metrics-server stack, without srsRAN Project gnb and Open5g services.

However, any service declared in the docker-compose.yml can be started standalone, like `core` or `influxdb`.

# GUI Services
This section describes the services which expose Web GUI so that you can access.
The services are binded to `127.0.0.1` by default for security reason. Thus if you need to access to them remotely, either using SSH port forwarding or update the IPs in [docker-compose.yml](docker-compose.yml) file.

For example, if the server is available at `192.168.0.122`, then you need to SSH to it:

```bash
# do not forget to replace your server IP
ssh -L3000:localhost:3000 -L3001:localhost:3001  -L3002:localhost:3002  -L3003:localhost:3003  -L3004:localhost:3004  -L31057:localhost:31057 montimage@192.168.0.122
```

## Open5GS
- expose port: `3003`, e.g., http://localhost:3003
- username/password: `admin`/`1423`
- document: https://open5gs.org/open5gs/docs/guide/01-quickstart/ (Section 3)

## Metric UI Setup
- export port: `3001`, e.g., http://localhost:3001
- no username/password

## MMT-Operator
- expose port: `3002`, e.g., http://localhost:3002
- username/password: `admin`/`mmt2nm`
- document: https://github.com/Montimage/mmt-operator

## MAIP
- expose port: `3000`, e.g., http://localhost:3000
- document: https://github.com/montimage/maip

## TaS
- expose port: `3004`, e.g., http://localhost:3004
- document: https://github.com/montimage/tas

