# Starterkit Deployment System
These scripts will deploy a StarterKit based project onto a server using
Ansible. Although these scripts can be run stand alone, it is assumed, that
they are located and executed within a StarterKit repository.

## Usage
When the deploy playbook is called, you must pass in:

- the type of deployment (init = first time, migrate = updating and existing site, finalize = setting the candidate to the current build, rollback = restoring the previous build)

- the host

- the ssh user

- the group name used by the webserver

- the name of the install profile (project name)

- the root database user

- the root database password

- the project database user

- the project database password

- the database name

- the Drupal adminstrative user

- the Drupal root user password

- the target folder

- the backup folder

- the local project folder

```ansible-playbook path/to/deploy/deploy.yml --tags="{init|migrate|finalize|rollback}"  --extra-vars="HOSTNAME={string} USER={string} GROUP={string} PROJECT_NAME={string} DB_SU={string} DB_SU_PW={string} DB_USER={string} DB_PW={string} DB_NAME={string} DRUPAL_SU_PW={string} TARGET_FOLDER={path} BACKUP_FOLDER={path} LOCAL_PROJECT_FOLDER={path}"```

## Tasks

- **init**

  - Create target folder {TARGET_FOLDER}

  - Create builds folder {TARGET_FOLDER}/builds

  - Create candidate folder {TARGET_FOLDER}/builds/candidate

  - Create current folder {TARGET_FOLDER}/builds/current

  - Create previous folder {TARGET_FOLDER}/builds/previous

  - Create old folder {TARGET_FOLDER}/builds/old

  - Create shared folder {TARGET_FOLDER}/shared

  - Set permissions (user and webserver group)

  - Archive project / Upload / Unpack in candidate folder

  - Symlink htdocs to candidate

  - Install profile

  - Update admin password

  - Move files and settings.php into shared folder and symlink files
    and settings.php to shared

  - Run finalize tasks


- **migrate**

  - Archive project / Upload / Unpack in candidate folder

  - Symlink htdocs to candidate

  - Put site offline

  - Backup site

  - Symlink {TARGET_FOLDER}/builds/candidate/htdocs/sites/default/settings.php
      to {TARGET_FOLDER}/shared/settings.php

  - Symlink {TARGET_FOLDER}/builds/candidate/htdocs/sites/default/files
      to {TARGET_FOLDER}/shared/files

  - Symlink {TARGET_FOLDER}/htdocs to {TARGET_FOLDER}/builds/candidate

  - Run drush updb

  - Bring site online


- **finalize**

  - IF exists {TARGET_FOLDER}/builds/old -> rm -rf /old

  - IF exists {TARGET_FOLDER}/builds/previous -> mv /previous /old

  - IF exists {TARGET_FOLDER}/builds/current -> mv /current /previous

  - mv {TARGET_FOLDER}/builds/candidate /current

  - Update symlink from candidate to current (ln -sfn current/ htdocs/)

  - Symlink the current build's files and settings to shared

  - Bring site online


- **rollback**

  - Symlink htdocs to {TARGET_FOLDER}/builds/current (ln -sfn)

  - Drop DB

  - Restore backup DB

  - rm -r {TARGET_FOLDER}/builds/candidate
