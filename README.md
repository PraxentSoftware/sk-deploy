# Starterkit Deployment System
These scripts will deploy a StarterKit based project onto a server using
Ansible. Although these scripts can be run stand alone, it is assumed, that
they are located and executed within a StarterKit repository.

## Usage

```ansible-playbook path/to/deploy/deploy.yml --tags="{init|migrate}" --connection="{HOST}" --extra-vars="target_folder={path}, backup_folder={path}"```

## Tasks

- **init**

  - Create target folder {TARGET_FOLDER}

  - Create builds folder {TARGET_FOLDER}/builds

  - Create shared folder {TARGET_FOLDER}/shared

  - Create {BACKUP_FOLDER}

  - Create vhost

  - Run upload

  - Run finalize


- **upload**

  - Tarball current project

  - Upload archive and unpack to {TARGET_FOLDER}/builds/candidate


- **backup**

  - Dump DB to {BACKUP_FOLDER}

- **restore**

  - Dump DB

  - Load DB from {BACKUP_FOLDER}

- **deploy**

  - Run backup

  - Run upload

  - Symlink {TARGET_FOLDER}/builds/candidate/htdocs/sites/default/settings.php
      to {TARGET_FOLDEr}/shared/settings.php

  - Symlink {TARGET_FOLDER}/builds/candidate/htdocs/sites/default/files
      to {TARGET_FOLDEr}/shared/files

  - Put site offline

  - Symlink {TARGET_FOLDER}/htdocs to {TARGET_FOLDER}/builds/candidate

  - Run drush updb

  - Bring site online

- **finalize**

  - IF exists {TARGET_FOLDER}/builds/old -> rm -r /old

  - IF exists {TARGET_FOLDER}/builds/previous -> mv /previous /old

  - IF exists {TARGET_FOLDER}/builds/current -> mv /current /previous

  - mv {TARGET_FOLDER}/builds/candidate /current

- **rollback**

  - rm htdocs; symlink htdocs to {TARGET_FOLDER}/builds/current

  - Drop DB

  - Run restore

  - rm -r {TARGET_FOLDER}/builds/candidate
