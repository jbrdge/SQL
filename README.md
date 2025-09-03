# Project: Curated LEGO Inventory Database

## Tools Used
- PHP, JavaScript, HTML  
- MySQL (via MAMP)  
- MySQL Bash Command Line tools  
- phpMyAdmin  

---

## Overview

This project builds a **curated LEGO inventory database** using data collected from the [LEGO Database on Kaggle](https://www.kaggle.com/rtatman/lego-database):  

> *“This dataset contains the LEGO Parts/Sets/Colors and Inventories of every official LEGO set in the Rebrickable database.”*  

The database is hosted locally in **MAMP** and accessed through a custom PHP/HTML/JS front-end.  

**Current functionality includes:**
- Sorting tables by column values  
- Displaying relational connectivity (e.g. `Set Name → Theme ID → Theme Name`)  
- Drop-down menus for navigation  
- Default views limited to the first 20 rows (except the `colors` table)  

---

## Screenshots

<p><img align="left" src="https://raw.githubusercontent.com/jbrdge/PHP/master/PHP-001.png" width="45%"></p>
<p><img align="left" src="https://raw.githubusercontent.com/jbrdge/PHP/master/PHP-002.png" width="45%"></p>

<br clear="both"/>

### Database Schema

<p><img src="https://github.com/jbrdge/PHP/blob/master/Lego%20SQL%20DATABASE/LegoDatabase/1599/downloads_schema.png" width="100%"></p>

---

## Setup Notes

### Increasing Upload Limits in phpMyAdmin
By default, phpMyAdmin restricts uploads to **8 MB**. To allow larger CSVs, edit the `php.ini` file:  

```ini
upload_max_filesize = 64M
post_max_size       = 64M
max_execution_time  = 3000
memory_limit        = 512M
```

Even after this, large (~10 MB) files still had issues in phpMyAdmin, so I switched to the MySQL CLI.

---

## Accessing MySQL via CLI
```bash
/Applications/MAMP/Library/bin/mysql --host=localhost -u root -p
```

## Creating a Table
```sql
CREATE TABLE InventoryP (
  inventory_id INT,
  part_num     VARCHAR(16),
  color_id     INT,
  quantity     INT,
  is_spare     VARCHAR(1)
);
```

## Importing Data
```sql
LOAD DATA INFILE '/path/to/inventory_parts.csv'
INTO TABLE InventoryP
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

## Fixing `secure_file_priv` Restriction
Error encountered:
```vbnet
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option
```
Check restriction:
```sql
SELECT @@GLOBAL.secure_file_priv;
```
If the result is `NULL`, configure MySQL to allow imports from a safe directory. Create/edit `~/.my.cnf`:
```ini
[mysqld_safe]
[mysqld]
secure_file_priv="/Users/me/"
```
Restart MySQL and confirm:
```sql
SELECT @@GLOBAL.secure_file_priv;
```
Result:
```bash
/Users/me/
```

✅ CSV upload now works.
---
## Cleaning Up Header Rows
```sql
DELETE FROM InventoryP WHERE part_num = 'part_num';
```
(Adjust column name and value as needed.)

## Key Takeaways
- Increased PHP upload size to support large CSV files
- Used MySQL CLI to bypass phpMyAdmin limits
- Adjusted secure_file_priv to enable local file imports
- Cleaned data to ensure correct table structure

