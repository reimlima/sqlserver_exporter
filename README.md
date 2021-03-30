# sqlserver_exporter

A quite simple prometheus exporter to collect metrics from SQL Servers.

## Badges

[![python][python-badge]][python-version] ![pylint-score]

## Requirements

* **python >= 3.6.8**
*  **gcc-c++**
* **msodbcsql**
* **unixODBC**
* **unixODBC-devel**

## Resolving Dependencies

Follow instructions in "[Install Tools]" documentation in Microsoft's site to add repository according to your OS.

Adjust URI to match your system's version.

Install these packages: gcc-c++ msodbcsql unixODBC unixODBC-devel

Then, run:

```sh
pip3 install -r requirements.txt
```

After that, edit you **/etc/odbc.ini** like the content below:

```
[your-sql-server]
Driver=ODBC Driver 13 for SQL Server
Description=your-sql-server
Trace=No
Server=x.x.x.x # <- Your Server IP here
```

## Config File

Just fill those options below in the sqlserver_exporter.cfg file:
```
[exporter_cfg]
exporter_port=9561 # Change here if you need
[db_login]
sqlserver_user=
sqlserver_password=
[db_dsns]
sqlserver_dsn=
```

### Considerations

* you can add many servers in *sqlserver_dsn*, just separete names by comma.
* create the same user with limited access to all databases.
* config file already have many queries to collect data, to turn one of them off, just comment the line with "#".
* this exporter requires no paramaters to start, just ensure to change "CONFIG_FILENAME" with the path where the config file is.

## Runnig the Exporter

Copy the file [sqlserver_exporter.service](sqlserver_exporter.service) to */etc/systemd/system*, make sure the exporter is in the path defined in the *ExecStart* option, then run the command below to ensure the exporter will start at System Boot:

```sh
$ systemctl enable sqlserver_exporter.service
```

Then run this command to start the process:

```sh
$ systemctl start sqlserver_exporter.service
```

Confirm it's running properlly:

```sh
$ systemctl status sqlserver_exporter.service
```

### See the metrics

If everything goes well, you should be able to see your metrics in:

> http://your-server:9561/metrics

## Prometheus

Add the job in Prometheus with:

```yml
- job_name: 'sqlserver_exporter'
    metrics_path: '/metrics'
    static_configs:
      - targets:
        - yourserver
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: your-server:9561
```

## Grafana Dashboard

The file [sqlserver_exporter-dashboard.json](sqlserver_exporter-dashboard.json) has a Panel for every metric collected by this exporter and can be imported to your Grafana to monitor your servers.

## Maintainer

[Reinaldo Lima]

## License

See [LICENSE](LICENSE) file

[//]: #
[python-badge]: https://img.shields.io/badge/python-3.6.8-blue
[python-version]: https://www.python.org/downloads/release/python-368/
[pylint-score]: https://mperlet.github.io/pybadge/badges/8.98.svg
[Install Tools]: https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-tools?view=sql-server-ver15#RHEL
[Reinaldo Lima]: https://github.com/reimlima
