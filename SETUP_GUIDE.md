# Portfolio Site Setup Guide

This guide covers the branch structure, how to update to a new release, and how to launch the site locally for development.

## Branch Structure

This repo uses three local branches, each with a clear role:

- **master** — The production branch. It tracks a specific release tag of the [al-folio](https://github.com/alshedivat/al-folio) template. GitHub Pages deploys from this branch. Keep it clean and only merge tested changes from `dev`.

- **dev** — The working branch. All customization and content editing happens here. It stays in sync with `master` after each release update. Test everything locally on this branch before merging into `master`.

- **archive-v1** — A snapshot of old, unused content from a previous version of the site. Kept as a reference in case anything needs to be recovered. Not actively maintained.

## How to Update to a New Release

When a new version of al-folio is released, follow these steps to update your local branches.

### 1. Fetch the latest tags from upstream

```bash
git fetch upstream --tags
```

### 2. Check the newest release

```bash
git tag --sort=-v:refname | head -5
```

Pick the latest tag (e.g. `v0.16.3`).

### 3. Reset master to the new release

```bash
git checkout master
git reset --hard <tag>
```

Replace `<tag>` with the actual version, for example `v0.16.3`.

### 4. Sync dev with master

```bash
git checkout dev
git reset --hard master
```

> **Note:** This discards any uncommitted work on `dev`. Commit or stash your changes first if needed.

### 5. Pull the matching Docker image

First, remove the old al-folio image to free up space (replace `<old-tag>` with the previous version):

```bash
docker image rm amirpourmand/al-folio:<old-tag>
```

Then pull the new one:

```bash
docker compose pull
```

This downloads the pre-built image that matches the release version specified in `docker-compose.yml`.

## Local Launch Guide

The site runs locally through Docker. This gives you live preview with auto-reload as you edit files.

### Prerequisites

- [Docker Desktop](https://docs.docker.com/get-docker/) installed and running (whale icon visible in menu bar)
- On macOS, install via `brew install --cask docker`

### Steps

#### 1. Switch to the dev branch

```bash
git checkout dev
```

#### 2. Pull the Docker image (first time or after a release update)

```bash
docker compose pull
```

#### 3. Start the local server

```bash
docker compose up
```

Wait for the output to show something like:

```
Server address: http://0.0.0.0:8080/...
```

#### 4. View your site

Open `http://localhost:8080` in your browser.

#### 5. Edit and preview

Change any file in the project. Jekyll rebuilds automatically. Refresh your browser to see updates.

#### 6. Stop the server

Press `Ctrl+C` in the terminal, then run:

```bash
docker compose down
```

### Troubleshooting

**Port already in use** — Another process may be using port 8080. Stop it or change the port in `docker-compose.yml`.

**Gem errors after updating** — Delete `Gemfile.lock` and rebuild:

```bash
rm Gemfile.lock
docker compose up --build
```
