# Tutorial4-Bash&GithubActions

# Lecturer:William Wang

# Agenda

## Regex
## Jenkins install





## Regex
ref link:<https://github.com/AutomationLover/Devops/blob/main/t04/Regex.md>

Regex

1. **Question:** Write a regex to match an IPv4 Address found in logs.
   **Target Log Entry:** `User with IP 192.168.1.1 failed to log in`
   **Answer:** `((25[0-5]|2[0-4][0-9]|1[0-9][2-9]|[1-9]?[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[0-9][2-9]|[1-9]?[0-9])`

2. **Question:** Write a regex to match log entries with a specific date format: `YYYY-MM-DD HH:MM:SS`.
   **Target Log Entry:** `2022-03-14 12:30:45 - Server started`
   **Answer:** `\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}`

3. **Question:** Write a regex to match lines in a log file that start with 'ERROR'.
   **Target Log Entry:** `ERROR - Database connection failed at 2022-03-14 12:30:45`
   **Answer:** `^ERROR.*$`

4. **Question:** Write a regex to match a timestamp in the format `HH:MM:SS`.
   **Target Log Entry:** `INFO - 2022-03-14 18:30:45 - User logged in`
   **Answer:** `([01]\d|2[0-3]):([0-5]\d):([0-5]\d)`

5. **Question:** Write a regex to match the HTTP response status code in a log.
   **Target Log Entry:** `GET /index.html 404`
   **Answer:** `\b\d{3}\b`
   
6. **Question:** Write a regex to match log entries containing a URL.
   **Target Log Entry:** `INFO - User redirected to http://example.com`
   **Answer:** `https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)`

7. **Question:** Write a regex to match the entries with word 'Exception' in the log file.
   **Target Log Entry:** `ERROR - 2022-03-14 18:30:45 - NullPointer Exception`
   **Answer:** `Exception`

8. **Question:** Write a regex to match entries in a log file that indicate a database connection error.
   **Target Log Entry:** `ERROR - 2022-03-14 18:30:45 - Database connection error`
   **Answer:** `(Database|DB) connection (error|failed)`

9. **Question:** Write a regex to match a log level mentioned in a log entry (e.g., `INFO`, `DEBUG`, `ERROR`, `WARNING`).
   **Target Log Entry:** `WARNING - 2022-03-14 18:30:45 - Disk is almost full`
   **Answer:** `(INFO|DEBUG|ERROR|WARNING)`

10. **Question:** Write a regex to match entries with memory leak error in the log file.
   **Target Log Entry:** `ERROR - 2022-03-14 18:30:45 - Memory leak detected`
   **Answer:** `memory leak`

## Jenkins install

ref link: <https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK4_Jenkins_and_GitHub_Actions/Wk4.1>
    
