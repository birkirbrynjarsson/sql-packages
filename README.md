# sql-packages
Automated database deployment tool that runs 'SqlPackage' from 'Microsoft' based on a JSON configuration file

## Requirements

`ms-sql-deploy` requires an installation of [_SqlPackage_](https://docs.microsoft.com/en-us/sql/tools/sqlpackage-download?view=sql-server-2017) and [_SqlCmd_](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-2017)
from _Microsoft_

`SqlPackage` should be accessible from a directory on `PATH` or installed at `%ProgramFiles%\Microsoft Sql Server\150\DAC\bin\SqlPackage.exe` on _Windows_.
`SqlCmd` should be accessible from a directory on `PATH` or installed at `%ProgramFiles%\Microsoft Sql Server\110\Tools\Binn\SQLCMD.exe` on _Windows_.

## Installation

Download the latest build for your OS

## Usage

The executable requires a `.json` configuration file as input,
each root element of the JSON document runs a single execution of `sqlpackage`
with the arguments for the execution defined within.  
All the possible `sqlpackage` arguments can be found on [_Microsoft's_ website](https://docs.microsoft.com/en-us/sql/tools/sqlpackage?view=sql-server-2017)

Run the executable with your deployment configuration:

```bash
sqlpackages deploy-config.json
```

Example configuration file: `deply-config.json`

```json
{
    "Erp2009_lite-destroy": {
            "Action": "sqlcmd",
            "S": "localhost",
            "U": "SA",
            "P": "12345678!",
            "Q": "IF EXISTS(SELECT * FROM sys.databases WHERE name='nav2009_lite') BEGIN ALTER DATABASE [nav2009_lite] SET SINGLE_USER WITH ROLLBACK IMMEDIATE; DROP DATABASE [nav2009_lite]; END"
        },
        "Erp2009_lite-deploy": {
            "Action": "Import",
            "SourceFile": "nav2009_lite.bacpac",
            "TargetServerName": "localhost",
            "TargetDatabaseName": "nav2009_lite",
            "TargetUser": "SA",
            "TargetPassword": "12345678!",
            "p": {
                "Storage": "File"
            }
        },
    "Production": {
        "Action": "Publish",
        "SourceFile": "development/dev_prod/bin/Debug/dev_prod.dacpac",
        "TargetDatabaseName": "prod",
        "TargetServerName": "localhost",
        "TargetUser": "SA",
        "TargetPassword": "12345678!",
        "p": {
            "IncludeCompositeObjects": true,
            "CompareUsingTargetCollation": true,
            "CreateNewDatabase": true
        }
    },
    "Staging": {
        "Action": "Publish",
        "SourceFile": "development/dev_stg_nav/bin/Debug/dev_stg_nav.dacpac",
        "TargetDatabaseName": "stg_nav",
        "TargetServerName": "localhost",
        "TargetUser": "SA",
        "TargetPassword": "12345678!",
        "p": {
            "IncludeCompositeObjects": true,
            "CompareUsingTargetCollation": true,
            "CreateNewDatabase": true
        }
    }
}
```

## Build & Development
The project uses `pipenv` for managing packages and virtual environment.

If you havent already, install `pipenv` with `pip` or optionally `brew` (MacOS)
```bash
pip install pipenv
```

Install packages:
```bash
pipenv install
```

Run the program within the virtualenv
```bash
pipenv run python app.py
```

Build the program from within the virtualenv with `pyinstaller`
```bash
pipenv shell
pyinstaller --onefile app.py -n sqlpackages
exit
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)
