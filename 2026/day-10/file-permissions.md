# Day 10 Challenge

## Files Created

* `devops.txt` – empty file created using `touch`
* `notes.txt` – file created with text content
* `script.sh` – shell script containing `echo "Hello DevOps"`
* `project/` – directory created for testing permissions

---

## Permission Changes

* `script.sh`

  * Before: `-rw-r--r--`
  * After: `-rwxr-xr-x` (made executable)

* `devops.txt`

  * Before: `-rw-r--r--`
  * After: `-r--r--r--` (read-only, removed write permissions)

* `notes.txt`

  * Before: `-rw-r--r--`
  * After: `-rw-r-----` (permission set to `640`)

* `project/`

  * Permissions set to `755` → `drwxr-xr-x`

---

##
