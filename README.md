# Introduction

Sample report designed for NetXMS reporting module, which shows all custom input widgets provided by the GUI.

Input field type is controlled by the custom property `logicalType` on each parameter. All currently supported types are included in this sample report.

# Package structure

Each report definition should be packaged into jar/zip with entry point `main.jrxml`. Once definition archive is loaded by the reporting server it will be automatically unpacked (while keeping folder structure) and compiled into `main.jasper`. Any sub reports will be compiled on execution. Property `SUBREPORT_DIR` points to the unpacked folder on execution (so you can refer to images or sub reports with `${SUBREPORT_DIR}/sub.jasper`).

Each report should contain manifest (`/META-INF/MANIFEST.MF`) with GUID `Build-Id`. This GUID is used to distinguish between deployed reports.

Name of the report is taken from `name` attribute of the `<jasperReport>` tag in `main.jrxml`.

If files `i18n.properties` or `i18n_LANG.properties` is found, descriptions for input fields will be loaded from the property file. User selected language in the management console controls locale.

# Build

While it's possible to create archives by hand (just zip `main.jrxml` and manifest), it's way more convenient to use Apache Maven for that:

`mvn package`

This will produce `target/sample-1.0.0.jar` which can be deployed to the reporting server.

# Complete sample

Build and deploy sample report, enable reporting module in the NetXMS:

```shell
# build report
git clone https://github.com/netxms/sample-jasper-report.git
cd sample-jasper-report
mvn package

# setup temporary workspace
mkdir -p /tmp/netxms-reporting/definitions

# copy report definition to correct location
cp target/sample-1.0.0.jar /tmp/netxms-reporting/definitions

# enable reporting module
nxdbmgr set EnableReportingServer 1 # enable reporting server connector
nxdbmgr set ReportingServerHostname 127.0.0.1 # point netxmsd to correct reporting server address
systemctl restart netxmsd # restart netxmsd to apply these changes

# start reporting server
nxreportd -w /tmp/netxms-reporting
```

Once all this is done, open "Reports" perspective in the management console and try executing the report.

# Making changes

[Jaspersoft® Studio](https://community.jaspersoft.com/project/jaspersoft-studio) can be used to modify or create new reports. To modify this one, right click on the project explorer, select `Import ➔ Existing Projects into Workspace`, then point root directory to the location where this repository is cloned. Once imported – open `main.jrxml` and start editing.

Most of the repots can be preview successfully from the designer, however you should provide input parameters in the correct form (specifically this can be an issue for lists).

