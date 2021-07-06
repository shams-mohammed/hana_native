# Getting Started

Welcome to your new project.

It contains these folders and files, following our recommended project layout:

File or Folder | Purpose
---------|----------
`app/` | content for UI frontends goes here
`db/` | your domain models and data go here
`srv/` | your service models and code go here
`package.json` | project metadata and configuration
`readme.md` | this getting started guide


## Next Steps

- Open a new terminal and run `cds watch` 
- (in VS Code simply choose _**Terminal** > Run Task > cds watch_)
- Start adding content, for example, a [db/schema.cds](db/schema.cds).


## Learn More

Learn more at https://cap.cloud.sap/docs/get-started/.




Create a SAP HANA Cloud instance named 'HANA_Cloud_Trail-hana' and subscribe SAP Business Application Studio.


hana BTP
DBADMIN

pwd
ls
npm config list
npm install -g hana-cli
hana-cli version
cds version
npm i -g @sap/cds-dk
cd projects
ls
cds help init
cds init hana_cloud --add hana,mta,pipeline

create a file ‘schema.cds’ in the root of ‘db’ folder:
entity Base {
    key ID: Integer;
}

pwd >> hana_cloud
npm install

In package.json >>
    "scripts": {
        "start": "npm cds run"
    }

cds build

updated ‘cds’ in package.json >>
 "cds": {
        "requires": {
            "db": {
                "kind": "sql"
            }
        },
        "hana": {
            "deploy-format": "hdbtable"
        },
        "build": {
            "target": ".",
            "tasks": [
                {
                    "src": "db",
                    "for": "hana",
                    "options": {
                        "model":[
                            "db",
                            "srv"
                        ]
                    }
                },
                {
                    "src": "srv",
                    "for": "node-cf",
                    "options": {
                        "model":[
                            "db",
                            "srv"
                        ]
                    }
                }
            ]
        }
    }

cds build
pwd
hana-cli help
hana-cli createModule

DELETE ‘gen’ folder from the root of project

Remove ‘gen/’ of path(in two places) in mta.yaml

cf services

cf create-service hana hdi-shared hana_cloud-db

cf services

cf create-service-key hana_cloud-db default
ls
hana-cli servicekey hana_cloud-db default
(set all ‘true’ for further parameters)

In root’s package.json file >>
Under scripts:
"env": "cp ./default-env.json ./db/default-env.json && cp ./default-env.json ./srv/default-env.json"

npm run env
cd db
hana-cli status
hana-cli systeminfo
npm install


In root’s package.json file >>
Under scripts:
“start”: “cds run”,
"build": "cds build/all —clean"

npm install
npm start
hana-cli opendbx

Add HDI Container >> search & select hana_cloud-db and check for ‘Tables’ >> ‘BASE’ table should be present.

In schema.cds >> 
TEST: String;

cd ..
cds build
cd db
npm start

02>>>>>>>>>>>>>>


Create a folder ‘schema’ to the root of ‘db’ folder and create a ‘common.cds’ file.

update schema.cds >> 
using from './schema/common';

In common.cds file >>
using {
    sap,
    Currency,
    temporal,
    managed
} from '@sap/cds/common';

extend sap.common.Currencies with {
    // Currencies.code = ISO 4217 alphabetic three-letter code
    // with the first two letters being equal to ISO 3166 alphabetic country codes
    // See also:
    // [1] https://www.iso.org/iso-4217-currency-codes.html
    // [2] https://www.currency-iso.org/en/home/tables/table-a1.html
    // [3] https://www.ibm.com/support/knowledgecenter/en/SSZLC2_7.0.0/com.ibm.commerce.payments.developer.doc/refs/rpylerl2mst97.htm
    numcode  : Integer;
    exponent : Integer; //> e.g. 2 --> 1 Dollar = 10^2 Cent
    minor    : String; //> e.g. 'Cent'
}

// to be removed
context opensap.common {
    entity Amount {
        currency : Currency;
    }
}

cds build
cd db
npm start
hana-cli opendbx

create a ‘_i18n’ folder to the root of project and create a i18n.properties into that folder. 
And copy the content from the openSAP hana GitHub. 

cd ..
cds build

Create a ‘csv’ folder to the root of ‘db’ and create a ‘sap.common-Currencies.csv’ file and copy its content from the openSAP hana GitHub. Create another ‘sap.common-Currencies_texts.csv’ and copy the content into it.

cds build
cd db
npm start

Check SAP_COMMON_CURRENCIES and its texts via ‘openData’ option in dbx.

Select Views from ‘Dev DB’ and check the view and analyse.

Update ‘common.cds’ file found in ‘db’ folder >
annotate temporal with {
  validFrom @(title : '{i18n>validFrom}');
  validTo   @(title : '{i18n>validTo}')
};

extend sap.common.Countries {
  code1           : Integer;
  alpha3          : String(3);
  iso             : String(16);
  region          : String(20);
  sub_region      : String(40);
  region_code     : String(3);
  sub_region_code : String(3);
  regions         : Composition of many sap.common_countries.Regions
                      on regions.country = $self.code;
}

annotate sap.common.Countries with {
  code1           @(
    title        : '{i18n>code1}',
    Common.Label : '{i18n>code1}'
  );
  alpha3          @(
    title        : '{i18n>alpha3}',
    Common.Label : '{i18n>alpha3}'
  );
  iso             @(
    title        : '{i18n>iso}',
    Common.Label : '{i18n>iso}'
  );
  region          @(
    title        : '{i18n>region}',
    Common.Label : '{i18n>region}'
  );
  sub_region      @(
    title        : '{i18n>sub_region}',
    Common.Label : '{i18n>sub_region}'
  );
  region_code     @(
    title        : '{i18n>region_code}',
    Common.Label : '{i18n>region_code}'
  );
  sub_region_code @(
    title        : '{i18n>sub_region_code}',
    Common.Label : '{i18n>sub_region_code}'
  );
};

context sap.common_countries {
	entity Regions {
	key country: String(3);
	key sub_code : String(5);
		toCountries: Association to one sap.common.Countries on toCountries.code = $self.country;
name: String(80);
type: String(80);
	}

  annotate Regions with {
    country  @(
      title        : '{i18n>country}',
      Common.Label : '{i18n>country}'
    );
    sub_code @(
      title        : '{i18n>sub_code}',
      Common.Label : '{i18n>sub_code}'
    );

    name     @(
      title               : '{i18n>name}',
      Common.FieldControl : #Mandatory,
      Search.defaultSearchElement,
      Common.Label        : '{i18n>name}'
    );
    type     @(
      title        : '{i18n>type}',
      Common.Label : '{i18n>type}'
    );
  }


Create new files ‘sap.common_countries-Regions-csv’ and ‘sap.common-Countries.csv’ in db/loads/common

cd ..
cds build
cd db
npm start


Update common.cds >>
In opensap.common >>>
context opensap.common {
    type BusinessKey : String(10);
    type SDate : DateTime;

    type AmountT : Decimal(15, 2)@(
        Semantics.amount.currencyCode : 'CURRENCY_code',
        sap.unit                      : 'CURRENCY_code'
    );

    type QuantityT : Decimal(13, 3)@(title : '{i18n>quantity}');
    type UnitT : String(3)@title : '{i18n>quantityUnit}';

    type StatusT : String(1) enum {
        New        = 'N';
        Incomplete = 'I';
        Approved   = 'A';
        Rejected   = 'R';
        Confirmed  = 'C';
        Saved      = 'S';
        Delivered  = 'D';
        Cancelled  = 'X';
    }

    aspect Amount {
        currency    : Currency;
        grossAmount : AmountT;
        netAmount   : AmountT;
        taxAmount   : AmountT;
    }

    annotate Amount with {
        grossAmount @(title : '{i18n>grossAmount}');
        netAmount   @(title : '{i18n>netAmount}');
        taxAmount   @(title : '{i18n>taxAmount}');
    }

    aspect Quantity {
        quantity     : QuantityT;
        quantityUnit : UnitT;
    }


    type Gender : String(1) enum {
        male         = 'M';
        female       = 'F';
        nonBinary    = 'N';
        noDisclosure = 'D';
        selfDescribe = 'S';
    }

    annotate Gender with @(
        title       : '{i18n>gender}',
        description : '{i18n>gender}',
        assert.enum
    );

    type Email : String(255)@title : '{i18n>email}'  @assert.format : '^([a-zA-Z0-9_\-\.]+)@([a-zA-Z0-9_\-\.]+)\.([a-zA-Z]{2,5})$';
    type PhoneNumber : String(30)@title : '{i18n>phoneNumber}'  @assert.format : '((?:\+|00)[17](?: |\-)?|(?:\+|00)[1-9]\d{0,2}(?: |\-)?|(?:\+|00)1\-\d{3}(?: |\-)?)?(0\d|\([0-9]{3}\)|[1-9]{0,3})(?:((?: |\-)[0-9]{2}){4}|((?:[0-9]{2}){4})|((?: |\-)[0-9]{3}(?: |\-)[0-9]{4})|([0-9]{7}))';
}

context sap.common_countries {

    entity Regions {
        key country     : String(3);
        key sub_code    : String(5);
            toCountries : Association to one sap.common.Countries
                              on toCountries.code = $self.country;
            name        : String(80);
            type        : String(80);
    }

    annotate Regions with {
        country  @(
            title        : '{i18n>country}',
            Common.Label : '{i18n>country}'
        );
        sub_code @(
            title        : '{i18n>sub_code}',
            Common.Label : '{i18n>sub_code}'
        );

        name     @(
            title               : '{i18n>name}',
            Common.FieldControl : #Mandatory,
            Search.defaultSearchElement,
            Common.Label        : '{i18n>name}'
        );
        type     @(
            title        : '{i18n>type}',
            Common.Label : '{i18n>type}'
        );
    }

}

define view iso_countries_regions as
    select from sap.common_countries.Regions {
        country          as COUNTRY_CODE,
        toCountries.name as COUNTRY_NAME,
        sub_code,
        name,
        type
    };

define view iso_us_states as
    select from sap.common_countries.Regions {
        sub_code,
        name
    }
    where
            country = 'US'
        and type    = 'State';

define view iso_us_states_and_territories as
    select from sap.common_countries.Regions {
        sub_code,
        name
    }
    where
        country = 'US';

define view counties as
    select from sap.common.Countries {
        @EndUserText.label : [{
            language : 'EN',
            text     : 'Country Name'
        }]
        name,
        @EndUserText.label : [{
            language : 'EN',
            text     : 'Country Code'
        }]
        code
    };


Create a file ‘conversions.cds’ in schema folder. Copy the content from openSAP GitHub to it. 

Create a folder ‘Conversions’ in ‘csv’ folder. Upload files >> T006.csv … TCURX.csv (copy from openSAP GitHub). 

Create a file ‘log.cds’ in ‘schema’ folder. and copy the content to it from openSAP GitHub.

Update schema.cds (found in the root of the project) >>
using from ‘./schema/conversions’;
using from ‘./schema/log’;

cds build

Update package.json (before “hana”) >>
“features”: {
	“snapi”: true	
}

cd db
npm start


create a file ‘masterData.cds’ in ‘schema’ folder and the content from the openSAP content (until Addresses entity). 

Upload the opensap.MD-Addresses.csv file from openSAP GitHub into ‘csv’ folder.

Update schema.cds file >>
using from ‘./schema/masterData’;

cd ..
cds build
cd ..
npm start

Check tables and views in DBX.

Update the masterData.cds file as per the openSAP GitHub.

Upload the files to ‘csv’ folder >> BusinessPartnet.csv, Employees.csv, Products.csv, Products_texts.csv


cd ..
cds build
cd db
npm start

Create a 'service.cds’ file in srv/gen folder and copy the content from openSAP GitHub to it. 

cds serve

Change Chrome language and analyze the OData response. 

>>>>>>>>>04
Create ‘purchaseOrder.cds’ file in ‘schema’ folder and copy the content to it from its GitHub. Also, update the schema.cds file with this new file. 

cds build

Uncomment the PurchaseOrder views in masterData.cds file. 

Upload files (PurchaseOrder-Headers and PrchaseOrder-Items.csv) to ‘csv’ folder.

cds build
cd db
npm start



Create a ’constraints’ folder in src and create a ‘PO_CONST.hdbconstraint’ file in it. Copy the content to it.

Create a new file ‘POHeaderItem.hdbconstraint’ and copy the content to it.

cd db
npm start

Create a ‘indexes’ folder in src and create a ‘partnerPOHeader.hdbindex’ file. Copy the content to it. 

Create a ‘data’ folder and create ‘LRep’ and ‘PurchaseOrder’ folders in it. 

Add userVariants.hdbview and variants.hdbtable files into ‘LRep’ folder. 
Copy the content to them. 

Add the complete files content to ‘PurchaseOrder’ folder completely.

cds build
cd db
npm start


>>>>>>>>>>>>>>>>05
Use DBX and open 'Open SQL Console' and copy the following to it. 

CREATE USER CUPS_SFLIGHT PASSWORD "<PASSWORD>" SET PARAMETER CLIENT = '001' SET USERGROUP DEFAULT;
ALTER USER CUPS_SFLIGHT DISABLE PASSWORD LIFETIME;
GRANT SELECT ON SCHEMA SFLIGHT TO CUPS_SFLIGHT WITH GRANT OPTION;
GRANT SELECT METADATA ON SCHEMA SFLIGHT to CUPS_SFLIGHT WITH GRANT OPTION;

CREATE ROLE SFLIGHT_CONTAINER_ACCESS;
GRANT SELECT, SELECT METADATA ON SCHEMA SFLIGHT TO SFLIGHT_CONTAINER_ACCESS WITH GRANT OPTION;
GRANT SFLIGHT_CONTAINER_ACCESS TO CUPS_SFLIGHT WITH ADMIN OPTION;

Add DB with different user (by rightClick on the DB on the left pane in DBX) --- CUPS_SFLIGHT

In BusinessApplicationStudio >> cf services
cf cups CROSS_SCHEMA_SFLIGHT -p "{\"user\":\"CUPS_SFLIGHT\",\"password\":\"<PASSWORD>\",\"driver\":\"com.sap.db.jdbc.Driver\",\"tags\":[\"hana\"] , \"schema\" : \"SFLIGHT\" }"
cf services

Update mta.yaml (in resources) as >>

# ------------------------------------------------------------
 - name: cross-container-service-1
# ------------------------------------------------------------  
   type: org.cloudfoundry.existing-service
   parameters:
     service-name: CROSS_SCHEMA_SFLIGHT
   properties:
     ServiceName_1: '${service-name}'   


Update default-env.json file (to the root of "hana") as >>>

        "user-provided": [
            {
                "label": "user-provided",
                "name": "CROSS_SCHEMA_SFLIGHT",
                "tags": [],
                "instance_name": "CROSS_SCHEMA_SFLIGHT",
                "binding_name": null,
                "credentials": {
                    "password": "<PASSWORD>",
                    "schema": "SFLIGHT",
                    "tags": [
                        "hana"
                    ],
                    "user": "CUPS_SFLIGHT"
                },
                "syslog_drain_url": "",
                "volume_mounts": []
            },
            {
                "label": "user-provided",
                "name": "sap.hana.democontent.epm.services.images",
                "tags": [],
                "instance_name": "sap.hana.democontent.epm.services.images",
                "binding_name": null,
                "credentials": {
                    "host": "www.loc.gov",
                    "port": "80",
                    "tags": [
                        "xshttpdest"
                    ],
                    "user": "CUPS_SFLIGHT"
                },
                "syslog_drain_url": "",
                "volume_mounts": []
            }
        ]

 Add befor VCAP_SERVICES in the same file as >>>

 "SERVICE_REPLACEMENTS": [
        {
            "key": "ServiceName_1",
            "service": "CROSS_SCHEMA_SFLIGHT"
        },
        {
            "key": "hdi-user-service",
            "service": "hana-opensap-cloud-2020-user-db"
        }
    ],

npm run env

Create a 'cfg' folder in db folder and create a file 'SFLIGHT.hdbgrants'.
And content as follows >>>

{
  "ServiceName_1": {
    "object_owner": {
      "roles": [
        "SFLIGHT_CONTAINER_ACCESS"
      ]
    },
    "application_user": {
      "roles": [
        "SFLIGHT_CONTAINER_ACCESS"
      ]
    }
  }
}

cd db
npm start

Create a folder 'synonyms' in the 'src' root folder and create a file sflight.hdbsynonym and add
the following content to it. 

{
  "SFLIGHT": {
    "target": {
      "object": "SFLIGHT",
      "schema": "SFLIGHT"
    }
  },
  "SBOOK": {
    "target": {
      "object": "SBOOK",
      "schema": "SFLIGHT"
    }
  },
  "SPFLI": {
    "target": {
      "object": "SPFLI",
      "schema": "SFLIGHT"
    }
  }
}

npm start

Verify synonyms in DBX in the HDI container >> synonyms and check their data.

Create a file SFLIGHT.cds in db/schema

hana-cli inspectTable SFLIGHT SFLIGHT

hana-cli inspectTable SFLIGHT SFLIGHT -o cds

hana-cli inspectTable SFLIGHT SBOOK -o cds

Copy above two command's content to the file above. The remaining content
from the openSAP GitHub.

Update schema.cds in db folder >> 
using from './schema/SFLIGHT';

cd ..
cds build

Create a folder SFLIGHT in db/src/data and a new file SflightView.hdbview
Copy the content to it from openSAP GitHub.

cd db
npm start

Analyse in DBX in HDB Container >> Views >> FLIGHT_SFLIGHTVIEW (created from cds),
FLIGHT_SFLIGHTEXT (which is restricted and created from cds)
and SflightView (created manually via hdbview).

Create a folder roles in db/src and a new file SFLIGHT_PRIV.hdbstructuredprivilege
and add the content to it from openSAP GitHub.

Create a new file sflight.hdbrole in roles folder and add the content as >>>
{
	"role":{
		"name": "sflight",
		"schema_analytic_privileges": [
            {
                "privileges":[ "FLIGHT_VIEW_PRIVILEGE" ]
            }
        ]
	}
}

Create another file admin.hdbrole and add the content from openSAP GitHub.
Create one more file adminOwner.hdbrole and add the content from openSAP GitHub.

Create a folder defaults in db/src and add a new file default_access_role.hdbrole and its content.

cd db
npm start




