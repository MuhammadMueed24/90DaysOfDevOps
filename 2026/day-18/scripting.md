# Task 1

<img width="443" height="582" alt="Screenshot from 2026-03-29 01-49-36" src="https://github.com/user-attachments/assets/af4fbb85-a528-4710-a000-c9b608a4f25e" />

# Task 2

<img width="858" height="589" alt="Screenshot from 2026-03-29 01-52-51" src="https://github.com/user-attachments/assets/24cea564-e2d2-41bb-8760-c9117b7dafcf" />

# Task 3

<img width="627" height="770" alt="Screenshot from 2026-03-29 01-54-47" src="https://github.com/user-attachments/assets/fc4ba9f7-c9e7-4b29-81c6-b4d22fd711ef" />

set -e →

Exit immediately if any command returns a non-zero (error) status.

Prevents the script from continuing after an error
Useful for catching failures early

Example:

ls /nonexistent   # script exits here
set -u →

Treat unset (undefined) variables as an error and exit.

Prevents using variables that were never defined
Helps catch typos and logic bugs

Example:

echo "$MY_VAR"   # error if MY_VAR is not set
set -o pipefail →

Makes a pipeline fail if any command in the pipeline fails.

Default behavior: only last command matters
With pipefail: entire pipeline fails if any part fails

Example:

cat file.txt | grep "text"

If cat fails, the pipeline fails (not just grep).

# Task 4

<img width="842" height="753" alt="Screenshot from 2026-03-29 01-55-59" src="https://github.com/user-attachments/assets/a808eadb-6f14-4f3a-b103-d71a72132004" />

# Task 5

<img width="859" height="981" alt="Screenshot from 2026-03-29 01-57-26" src="https://github.com/user-attachments/assets/4fd77cd6-8dff-4ae6-a23f-7a61ca5fc651" />



