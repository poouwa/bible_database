# Bible Databases for Mobile Apps

This repository contains SQLite databases of various Bible translations and versions, intended for use in mobile Bible applications. The `versions.json` file provides version and checksum information for each database file, enabling developers to easily manage updates and ensure data integrity.

## Contents

*   [Introduction](#introduction)
*   [Database Files](#database-files)
*   [versions.json](#versionsjson)
*   [How to Use](#how-to-use)
*   [Database Schema](#database-schema)

## Introduction

These databases are provided in SQLite format, a lightweight and portable database engine ideal for mobile development.  Each database file contains the text of a specific Bible translation and version.

## Database Files

The database files are named according to the following convention: `bible_ABBREVIATION.db`, where `ABBREVIATION` is the standard abbreviation for the Bible translation (e.g., `bible_kjv.db`, `bible_web.db`).

## versions.json

The `versions.json` file is a crucial component of this repository. It contains metadata about each database file, including:

*   `name`: The full name of the Bible translation.
*   `abbreviation`: The standard abbreviation.
*   `filename`: The name of the corresponding database file.
*   `version`: An integer representing the version of the database.  This number is incremented each time the database is updated.
*   `size`: The size of the database file in bytes.
*   `checksum`: A SHA-256 checksum of the database file.  This is **essential** for verifying the integrity of downloaded files.

## How to Use

1.  **Obtain the `versions.json` file:** Fetch the raw content of the `versions.json` file from the repository using its raw URL (e.g., `https://raw.githubusercontent.com/poouwa/bible_database/main/versions.json`).

2.  **Parse the `versions.json` file:** Parse the JSON data to get information about the available Bible versions and their corresponding database files.

3.  **Check for updates:** Compare the version numbers in the `versions.json` file with the versions of the databases currently stored on the user's device.

4.  **Verify checksums:** If an update is available, **before** downloading the new database file, verify its integrity by comparing the checksum in the `versions.json` file with the checksum of the existing local database file (if it exists). If the checksums don't match or the local file doesn't exist, download the database file using the raw URL provided in the `versions.json` file.

5.  **Store local version information:** After a successful download, update the locally stored version information to reflect the newly downloaded database.


## Database Schema

### `books` Table

| Column        | Type    | Constraints      | Description                               |
| ------------- | ------- | --------------- | ----------------------------------------- |
| id            | INTEGER | PRIMARY KEY     | Unique identifier for the book            |
| eng_name_id   | TEXT    | NOT NULL, UNIQUE | English name ID (e.g., "GENESIS")        |
| abbreviation  | TEXT    |                 | Standard abbreviation (e.g., "Gen")       |
| short_name    | TEXT    |                 | Shorter name (e.g., "Genesis")           |
| long_name     | TEXT    |                 | Full name (e.g., "The Book of Genesis") |
| language      | TEXT    | NOT NULL        | Language of the Bible text (e.g., "en") |
| order_number  | INTEGER | NOT NULL, UNIQUE | Canonical order of the book             |
| version       | INTEGER | NOT NULL, DEFAULT 1 | Version of the book record               |

### `chapters` Table

| Column         | Type    | Constraints      | Description                                     |
| -------------- | ------- | --------------- | ----------------------------------------------- |
| id             | INTEGER | PRIMARY KEY     | Unique identifier for the chapter               |
| book_id        | INTEGER | NOT NULL, FOREIGN KEY | Foreign key referencing the `books` table       |
| chapter_number | INTEGER | NOT NULL        | Chapter number                                 |
| UNIQUE         |         | (book_id, chapter_number) | Prevents duplicate chapters within a book      |

### `verses` Table

| Column         | Type    | Constraints      | Description                                     |
| -------------- | ------- | --------------- | ----------------------------------------------- |
| id             | INTEGER | PRIMARY KEY     | Unique identifier for the verse                 |
| chapter_id     | INTEGER | NOT NULL, FOREIGN KEY | Foreign key referencing the `chapters` table      |
| verse_number   | INTEGER | NOT NULL        | Verse number                                   |
| text           | TEXT    | NOT NULL        | The actual text of the verse                   |
| UNIQUE         |         | (chapter_id, verse_number) | Prevents duplicate verses within a chapter      |

### `book_metadata` Table

| Column      | Type    | Constraints | Description                               |
| ----------- | ------- | ----------- | ----------------------------------------- |
| book_id     | INTEGER | PRIMARY KEY, FOREIGN KEY | Foreign key referencing the `books` table       |
| introduction | TEXT    |             | Introduction text for the book            |
| outline     | TEXT    |             | Outline of the book                          |
| author      | TEXT    |             | Author of the book                         |
