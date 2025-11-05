Automated Backup System (Bash Scripting Project)
Project Overview

The Automated Backup System is a Bash script that creates, verifies, and manages backups of important folders automatically.
It’s like a smart “copy & paste” — it remembers what it copied, checks that everything is safe, and cleans up old files to save space.

This project demonstrates shell scripting, automation, and file management skills using core Linux commands such as tar, find, sha256sum, and date.

 Features

* Create compressed backups (.tar.gz) of any folder
* Skip unwanted files (.git, node_modules, .cache, etc.)
* Verify backups using SHA256 checksums
* Automatic cleanup — keeps only the latest daily, weekly, and monthly backups
* Configuration file support (backup.config)
* Detailed logging (backup.log)
* Dry run mode — preview actions before running
* Lock file — prevents multiple simultaneous runs
* Error handling — safe even if folders are missing or permissions fail

Bonus Features (optional):
 Restore from backup
 List available backups
 Simulated email notifications
 Disk space check
 Incremental backups

 Folder Structure
backup-system/
├── backup.sh           # Main Bash script
├── backup.config       # User configuration file
├── README.md           # Documentation (this file)
└── logs/
    └── backup.log      # Log file (auto-created)


Example backup folder:

/home/backups/
├── backup-2024-11-03-1430.tar.gz
├── backup-2024-11-03-1430.tar.gz.sha256
├── backup-2024-10-27-0900.tar.gz
└── ...

 Configuration File (backup.config)

Users can set all options here (no need to edit the script):

# Where to save all backup files
BACKUP_DESTINATION=/home/backups

# Patterns to exclude from backup
EXCLUDE_PATTERNS=".git,node_modules,.cache"

# Retention policy
DAILY_KEEP=7
WEEKLY_KEEP=4
MONTHLY_KEEP=3

 How to Use
1️ Make Script Executable
chmod +x backup.sh

2️ Run a Backup
./backup.sh /home/user/documents


This creates a file like:

backup-2024-11-03-1430.tar.gz
backup-2024-11-03-1430.tar.gz.sha256

3️ Dry Run Mode (No changes made)
./backup.sh --dry-run /home/user/documents


Example output:

Would create backup for: /home/user/documents
Would skip patterns: .git,node_modules,.cache
Would delete old backup: backup-2024-09-20-1010.tar.gz

4️ Verify Backup
./backup.sh --verify-only


Checks checksum and ensures the archive can be extracted.

5️ Restore Backup (Bonus)
./backup.sh --restore backup-2024-11-03-1430.tar.gz --to /home/user/restored_files

6️ List All Backups (Bonus)
./backup.sh --list

 How It Works
🔹 Step 1: Create Backup

Reads configuration and timestamp.

Uses tar to compress the source directory:

tar --exclude='.git' --exclude='node_modules' -czf backup-$TIMESTAMP.tar.gz "$SOURCE"


Saves a SHA256 checksum:

sha256sum backup-$TIMESTAMP.tar.gz > backup-$TIMESTAMP.tar.gz.sha256

🔹 Step 2: Verify Backup

Recalculates checksum and compares with saved .sha256 file.

Tests extraction of one sample file to confirm integrity.

🔹 Step 3: Delete Old Backups

Keeps:

7 most recent daily backups

4 most recent weekly backups

3 most recent monthly backups

Uses find and sort to identify older backups for deletion.

🔹 Step 4: Logging

Logs every action to backup.log:

[2024-11-03 14:30:15] INFO: Starting backup of /home/user/documents
[2024-11-03 14:30:45] SUCCESS: Backup created successfully
[2024-11-03 14:31:02] INFO: Checksum verified successfully
[2024-11-03 14:32:10] INFO: Deleted old backup: backup-2024-09-20-1010.tar.gz

🔹 Step 5: Lock File

Prevents simultaneous runs using:

LOCK_FILE=/tmp/backup.lock

 Design Decisions

Modular functions: Each feature (create, verify, delete) is in its own function.

Configuration-based: Easy for users to modify without editing code.

Checksum verification: Ensures backups are not corrupted.

Timestamp naming: Makes it easy to track and sort backups.

Log + lock system: Safe and transparent operations.

 Testing
 Test Cases
Test Case	Command	Expected Result
Create new backup	./backup.sh /home/user/test_folder	Backup file created, checksum verified
Dry run mode	./backup.sh --dry-run /home/user/test_folder	Shows planned actions, no backup created
Missing folder	./backup.sh /wrong/path	Error: Source folder not found
Check verification	./backup.sh --verify-only	SUCCESS message
Old backups cleanup	Run multiple times	Older backups deleted automatically
Lock file test	Run script twice	Second run blocked
Restore backup	./backup.sh --restore ...	Files extracted successfully

 Error Handling

The script safely handles:

Missing source folder → prints "Error: Source folder not found"

No permission to read folder

Missing config → uses default values

Low disk space → warns and exits

Partial backups → cleans up temporary files

Multiple runs → prevents with lock file

 Known Limitations

 Incremental backups not implemented (planned feature)
 Email notifications simulated by writing to a log file
 Restore feature works only for .tar.gz archives
 Requires manual date simulation to test daily/weekly/monthly rotation

 Example Outputs

Log File Example (backup.log):

[2024-11-03 14:30:15] INFO: Starting backup of /home/user/documents
[2024-11-03 14:30:45] SUCCESS: Backup created: backup-2024-11-03-1430.tar.gz
[2024-11-03 14:30:46] INFO: Checksum verified successfully
[2024-11-03 14:30:50] INFO: Deleted old backup: backup-2024-10-05-0900.tar.gz


Console Output Example:

 Backup completed successfully!
 File: backup-2024-11-03-1430.tar.gz
 Checksum verified
 Old backups cleaned
 Log saved to backup.log

 Conclusion

This project automates the entire backup lifecycle — creation, verification, and cleanup — in a configurable and reliable way.
It’s a great demonstration of Bash scripting, automation, error handling, and system administration skills.
