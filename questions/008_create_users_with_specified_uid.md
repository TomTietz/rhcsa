# User creation with specific properties

### Question:
Create two users: ***john*** with uid/gid equal to 2000, password 12345678 and ***davis*** with uid/gid equal to 3000, password 87654321. 
Make ***davis'*** password validity stopping in one month.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Create users
    ```
    useradd --uid 2000 --gid 2000 --password 12345678 john
    useradd --uid 3000 --gid 3000 --password 87654321 davis
    ```
2. Set expiration date `chage -E $(date -d +1month +%Y-%m-%d)`

### Additional comment
The safe and recommended way rto set the user password without storing it in bash history (bad) is to net set the password during account creation and using `passwd USERNAME` afterwards. But this works for demonstration purposes and I'm lazy.

Using "man chage" and scrolling down a bit you can copy and paste the command above without memorizing it and just simply paste the number of days you want to add until the account expires.
Remember there is a difference between **password expiration** and **account validation**. It is usually better to expire password and 
make user not being able to log into the system or disable the user, rather than deleting it.

**usermod** command is also used for adding/removing users to the group (**groupmod** command does not do that)

Trying to create user with already taken UID will result in an error.
 
During user's creation with **useradd** command the structure of home direcotry is taken from ***/etc/skel*** folder. 
