# FlashPaper
A one-time encrypted zero-knowledge password/secret sharing application focused on simplicity and security. No database or complicated set-up required.

![Docker Pulls](https://img.shields.io/docker/pulls/modem7/flashpaper) 
![Docker Image Size (tag)](https://img.shields.io/docker/image-size/modem7/flashpaper/latest)
[![Build Status](https://drone.modem7.com/api/badges/modem7/flashpaper/status.svg)](https://drone.modem7.com/modem7/flashpaper)
[![GitHub last commit](https://img.shields.io/github/last-commit/modem7/flashpaper)](https://github.com/modem7/flashpaper)

## Original Project

https://github.com/AndrewPaglusch/FlashPaper

## Demo

https://flashpaper.io

![Picture of Main Page](https://i.imgur.com/KIs9fjE.png)

## Installation

### Docker *(Recommended)*

[DockerHub](https://hub.docker.com/r/modem7/flashpaper)

```yaml
version: "2.4"
services:
  flashpaper:
    image: modem7/flashpaper
    container_name: Flashpaper
    restart: always
    volumes:
      - $USERDIR/Flashpaper:/var/www/html/data
    ports:
      - '8080:80'
    environment:
      SITE_TITLE: "FlashPaper :: Self-Destructing Message"
      RETURN_FULL_URL: "true"
      MAX_SECRET_LENGTH: "3000"
      ANNOUNCEMENT: ""
      MESSAGES_ERROR_SECRET_TOO_LONG: "Input length too long"
      MESSAGES_SUBMIT_SECRET_HEADER: "Create A Self-Destructing Message"
      MESSAGES_SUBMIT_SECRET_SUBHEADER: ""
      MESSAGES_SUBMIT_SECRET_BUTTON: "Encrypt Message"
      MESSAGES_VIEW_CODE_HEADER: "Self-Destructing URL"
      MESSAGES_VIEW_CODE_SUBHEADER: "Share this URL via email, chat, or another messaging service. It will self-destruct after being viewed once."
      MESSAGES_CONFIRM_VIEW_SECRET_HEADER: "View this secret?"
      MESSAGES_CONFIRM_VIEW_SECRET_BUTTON: "View Secret"
      MESSAGES_VIEW_SECRET_HEADER: "Self-Destructing Message"
      MESSAGES_VIEW_SECRET_SUBHEADER: "This message has been destroyed"
      PRUNE_ENABLED: "true"
      PRUNE_MIN_DAYS: 30
      PRUNE_MAX_DAYS: 60
```


## How It Works
### Submitting Secret
  1. `<random>--secrets.sqlite` sqlite database created (if it doesn't already exist)
  2. `<random>--aes-static.key` randomized 256-bit AES static key created (if one doesn't exist already)
  3. Random 256-bit AES key created
  4. Random 128-bit IV created
  5. Random 64-bit ID created
  6. ID + AES key hashed with bcrypt 
  7. Submitted text encrypted with AES-256-CBC using AES key and random IV
  8. Ciphertext now encrypted with AES-256-CBC using static AES key and random IV
  9. ID and AES key joined (known as `k`)
  10. Random prune date/time generated using `prune`->`min_days`/`max_days`
  11. ID, IV, bcrypt hash, ciphertext, and prune epoch stored in DB
  12. `k` value returned to user in one-time URL

### Retrieving Secret
  1. `k` value removed from URL
  2. `k` value split into two parts: ID and AES key
  3. IV, bcrypt hash, ciphertext looked up in DB with ID from `k`
  4. `k` bcrypt hash compared against bcrypt hash from DB (prevents tampering of URL)
  5. Ciphertext decrypted with static AES key and IV
  6. Ciphertext decrypted with AES key from `k` and IV
  7. Entry deleted from DB
  8. Decrypted text sent to user

## Settings

### `prune`:
 - `enabled`: Turn on/off auto-pruning of old secrets from the database upon page load
 - `min_days`/`max_days`: When a secret is submitted, a random date/time is generated between `min_days` and `max_days` in the future. After that date/time has elapsed, the secret will be pruned from the database if `enabled` is set to `true`. This is to prevent your database from being filled with secrets that are never retrieved. NOTE: Even if `enabled` is set to `false`, the prune value will still be generated and stored in the database, but secrets will not be pruned unless `enabled` is switched to `true`.
