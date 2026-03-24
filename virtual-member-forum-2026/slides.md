---
theme: default

title: Hands-On with ArchivesSpace
favicon: /small-logo.png
codeCopy: true
class: text-center

# slide transition: https://sli.dev/guide/animations.html#slide-transitions
# transition: slide-left
# enable Comark Syntax: https://comark.dev/syntax/markdown
comark: true
# duration of the presentation
duration: 45min
---

# Hands-On with ArchivesSpace

## Docker Installation, Customization, and API Integration
## March 2026

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# Presentation Outline

- Installing ArchivesSpace on a laptop / personal computer
- Initializing with some demo data
- Customization through configuration
- Installing plugins to extend functionality
- Database administration
- Search index administration
- Interacting with ArchivesSpace through the API

---

# Get Docker Desktop

## What is Docker
- Running applications using containers:
  - Lightweight, isolated environments
  - Can run on any infrastructure

## How to Install Docker Desktop
- https://docs.docker.com/get-started/get-docker/


---

# ArchivesSpace on Docker

## Get the ArchivesSpace Docker Configuration Package
- https://github.com/archivesspace/archivesspace/releases/tag/v4.1.1

## Documentation
- https://docs.archivesspace.org/administration/docker/

---

# Docker Conf Package contents

```ruby copy
.
├── backups       # created after starting ArchivesSpace to contain the database backups
├── config
│   └── config.rb # the main configuration of ArchivesSpace
├── locales       # yml files allowing customization of the UI text
├── plugins       # accommodates ArchivesSpace plugins
├── proxy-config
│   └── default.conf   # configuration of the bundled nginx proxy
├── sql                # placing an .sql file here will initialize the database
├── docker-compose.yml # the information required by Docker to run ArchivesSpace
└── .env               # configuration of the docker containers including
```

<!--
- backups directory: first created once you start the application. Will contain the automatically performed backups of the database. See Automated Backups section.
- config/config.rb: contains the main configuration of ArchivesSpace.
- locales: 
- plugins: accommodates additional ArchivesSpace plugins. By default contains the local and lcnaf plugins.
- proxy-config/default.conf: contains the configuration of the bundled nginx, see also proxy configuration.
- sql: put your .sql database dump file here to initialize the new database, see next section.
- docker-compose.yml: contains all the information required by Docker to build and run ArchivesSpace.
- .env: contains configuration of the docker containers including:
  - Credentials used by ArchivesSpace to access its MySQL database. Recommended to change default root and user passwords.
  - The database connection URI which should also be updated accordingly after the database user password is updated.
-->

---

# Initialize with Demo Data​

## Use our demo database

- Download into the ./sql directory
  - https://github.com/archivesspace/archivesspace/blob/master/build/mysql_db_fixtures/demo.sql.gz
  - click on the three dots `...` to download

- No need to decompress the file

---

# Fire up ArchivesSpace!​

- Change to the extracted directory and run:

    ```bash copy
    docker compose up --detach​
    ```

- Watch logs in Docker Desktop

- Remove the `demo.sql.gz` file from the `sql` directory

---

# Configuring ArchivesSpace​​

- Change text with locales
- Update the logo

---

# Change UI text with locales

- Let's update the Public UI home page text: http://localhost/
- Locales are exposed in the `locales` directory
- Edit `locales/public/en.yml` for the english text

    ```yaml copy
    en:
      brand:
        title: ArchivesSpace Public Interface
        title_link_text: Return to the ArchivesSpace homepage
        welcome_head: Welcome to ArchivesSpace
        welcome_message: |
          <p>Search across our collections, digital materials, and more.</p>
        welcome_page_title: ArchivesSpace Public Interface
    ```

- Restart `archivesspace` container on Docker Desktop

---

# Change PUI and SUI logos

## When using a URL

- Update config variables in `config/config.rb`

    ```ruby copy
    AppConfig[:pui_branding_img] = 'https://creativecommons.org/wp-content/uploads/2019/02/ccheart_black.svg_.zip'
    AppConfig[:frontend_branding_img] = 'https://creativecommons.org/wp-content/uploads/2019/02/ccheart_red.svg_.zip'
    ```

- See also https://docs.archivesspace.org/customization/theming/


---

# Change PUI and SUI logos



## When using a local file

- Update config variables in `config/config.rb`

    ```ruby copy
    AppConfig[:pui_branding_img] = '/assets/images/custom-logo.svg'
    AppConfig[:frontend_branding_img] = '/assets/images/custom-logo-staff.svg'
    ```
- Add your `custom-logo.svg` file in `plugins/local/public/assets/images`
- Add your `custom-logo-staff.svg` file in `plugins/local/frontend/assets/images`
- Files can also be in the `png` format - update filenames accordingly


---

# Let's install some Plugins

- Digitization Work Order Plugin
  - By Hudson Molonglo
  - Provides the ability to download reports for sets of components under a resource for the purpose of creating digitization work orders.

- Content Warnings Plugin
  - By Digital Library Technologies Group, Dartmouth College Library
  - Allows staff users to add content warnings and optional clarifying descriptions of why the warning has been applied.

---

# Plugin Installation overview​

- Place the plugin code in the plugins directory​
- Add plugin name to the conf file​
  - Order matters​
- Optionally (depending on the plugin requirements)
  - execute plugin initialization
  - run plugin database migrations
- Restart ArchivesSpace server​

---

# Digitization Work Order Plugin​
- Download and follow instructions:
  - https://github.com/hudmol/digitization_work_order

- Run commands on the container:
  - Docker Desktop -> archivesspace container -> Exec tab

    ```bash copy
    $ cd archivesspace
    $ ./scripts/initialize-plugin.sh digitization_work_order
    ```
---

# Content Warnings Plugin​

- Download and follow instructions:
  - https://github.com/dartmouth-dltg/aspace_content_warnings

- Run commands on the container:
  - Docker Desktop -> archivesspace container -> Exec tab
      ```bash copy
      $ cd archivesspace/​
      $ ./scripts/setup-database.sh​
      ```

- Add translations for new Controlled List value​
  - Locale files are in the `locales` directrory on the host system

---

# Database administration​​

- Backup in mysql container -> exec​ tab

    ```bash copy
    mysqldump -u root -p123456 archivesspace | gzip > /tmp/db.$(date +%F.%H%M%S).sql.gz​
    ```

- Download the file in the Files tab of the mysql container​
- Manipulate a resource record:
  - http://localhost/staff/resources/6/edit

- Restore: mysql container -> exec​ tab

    ```bash copy
    gunzip -c /tmp/db.2026-02-17.155254.sql.gz | mysql -uas -pas123 archivesspace​
    ```

<!--
- Backup:
  - https://docs.archivesspace.org/administration/backup/#using-the-docker-configuration-package

- Restore:
  - https://docs.archivesspace.org/administration/backup/#when-running-on-docker
  -->

---

# Database administration​ docs​


- Backup:
  - https://docs.archivesspace.org/administration/backup/#using-the-docker-configuration-package

- Restore:
  - https://docs.archivesspace.org/administration/backup/#when-running-on-docker


---

# Solr administration​
- Reindex​

  ```bash copy
  docker compose down solr app​
  docker volume rm archivesspace_app-data archivesspace_solr-data​
  docker compose up –d​
  ```
- Solr Admin interface​
  - http://localhost:8983/solr/#/

---

# ArchivesSpace API​​

- Get Postman
  - https://www.postman.com/downloads
- Documentation
  - Introduction
      - https://docs.archivesspace.org/api
  - API reference
    - https://archivesspace.github.io/archivesspace/api/#introduction
  - API Playbook - Introduction to API workshop
    - https://support.atlas-sys.com/hc/en-us/articles/360052217114-The-ArchivesSpace-API-Playbook​
    - https://www.youtube.com/watch?v=ajaYbtKR1BE&t=234s
---

# Using Postman

- Create an "ArchivesSpace collection
  - Collection Variables:
    - baseUrl: http://localhost/staff/api/
    - sessionToken: empty for now, we will get it from the login response
  - Authorization:
    - Auth type: API Key
      - Key: `X-ArchivesSpace-Session`
      - Value: `{{sessionToken}}`
      - Add to: Header

---

# Login

- Create a Login Request
  - Method: `POST`
  - URL: `{{baseUrl}}/users/admin/login?password=admin`
  - Click Send
  - Get the sessionToken from the response and update the `sessionToken` variable in the "ArchivesSpace" collection

---

# List Repositories

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/repositories`
  - Click Send
  - Get the first repository id from the response

---

# List Resources

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/repositories/2/resources?page=1`
  - Click Send

---

# List Accessions

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/repositories/2/accessions?page=1`
  - Click Send

---

# List Agent Families

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/agents/families?page=1`
  - Click Send

---

# Create an Agent Family

  - Create a new request
  - Method: `POST`
  - URL: `{{baseUrl}}/agents/families`
  - Body (raw)
    ```json copy
    {
      "jsonmodel_type": "agent_family",
      "dates_of_existence": [...
    ```

---

# Thank you

  - ArchivesSpaceHome@lyrasis.org
    - https://docs.archivesspace.org/


<style>
/* Indent h1 on all slides except the first (which has text-center) */
.slidev-layout:not(.text-center) h1 {
  padding-left: 3.5rem;
}
</style>
    
