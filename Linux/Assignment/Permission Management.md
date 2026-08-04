
---

# Linux Practical Assignment

## Topic: User Management, Permission Management, Password Management, SUID, SGID & Sticky Bit

### Rules

* सभी tasks CLI से करने हैं।
* GUI का उपयोग नहीं करना है।
* किसी भी task में `777` permission का उपयोग नहीं करना है।
* जहाँ root privilege की आवश्यकता हो, वहाँ `sudo` का उपयोग करें।
* हर task पूरा होने के बाद उसका verification भी करें।

---

# Question 1 - User Management & Password Management

Create the following users.

* `developer`
* `tester`
* `support`

Perform the following tasks:

1. Create all three users.
2. Set passwords for all users.
3. Force each user to change the password at the first login.
4. Lock the account of `support`.
5. Unlock the account again.
6. Verify that all users exist and display their account information.

---

# Question 2 - File Ownership & Basic Permissions

Using the users created in Question 1:

1. Create the directory

```bash
/project
```

2. Inside it create the following files.

```text
app.py
config.conf
deploy.sh
README.md
```

3. Change the ownership as follows.

| File        | Owner     |
| ----------- | --------- |
| app.py      | developer |
| config.conf | developer |
| deploy.sh   | tester    |
| README.md   | tester    |

4. Configure permissions so that

* Owner has Read, Write and Execute (where applicable).
* Others cannot modify any file.
* `deploy.sh` should be executable.
* `README.md` should be read-only for everyone except its owner.

5. Verify all permissions using appropriate Linux commands.

---

# Question 3 - Permission Modification Practice

Using the same `/project` directory:

Perform the following tasks.

1. Give only Read permission to Others on `config.conf`.
2. Remove Execute permission from Owner on `app.py`.
3. Add Execute permission only for Owner on `app.py`.
4. Remove Write permission for Group from all files.
5. Apply recursive permission so that every directory under `/project` becomes `755` and every regular file becomes `644`, except `deploy.sh`, which must remain executable.

Verify every change.

---

# Question 4 - SUID & SGID Practical

Create the following executable files.

```text
/usr/local/bin/resetpass
/usr/local/bin/sharedscript
```

Perform the following tasks.

1. Assign ownership of both files to `root`.
2. Enable **SUID** on `resetpass`.
3. Enable **SGID** on `sharedscript`.
4. Verify that both special permissions are applied correctly.
5. Display permissions in symbolic as well as numeric format.

---

# Question 5 - Sticky Bit Practical

Create a shared directory.

```bash
/shared-data
```

Perform the following tasks.

1. Allow every user to create files inside the directory.
2. Apply the **Sticky Bit**.
3. Login as `developer` and create a file.
4. Login as `tester` and try deleting the developer's file.
5. Verify whether deletion is allowed.
6. Display the permission of the directory.

---

# Question 6 - Complete Permission Scenario

Using the same users and directories created earlier:

Create the following structure.

```text
/company
├── developers
├── testing
└── backup
```

Perform the following tasks.

1. `developer` should have full control over `developers`.
2. `tester` should have full control over `testing`.
3. `support` should have only Read permission on both directories.
4. Nobody except root should be able to modify the `backup` directory.
5. Verify the access by switching between users.

---

# Question 7 - Password Management Practical

Perform the following tasks.

1. Change the password of `developer`.
2. Set password expiry to **30 days**.
3. Set warning period to **7 days**.
4. Set minimum password age to **5 days**.
5. Display password ageing information.

---

# Question 8 - Recursive Ownership & Permission Assignment

Inside `/project`, create the following structure.

```text
project/
├── src
├── logs
├── backup
└── scripts
```

Create at least **three files inside each directory**.

Now perform the following tasks.

1. Change ownership of the entire `/project` recursively to `developer`.
2. Make all directories `750`.
3. Make all files `640`.
4. Only files inside `scripts` should be executable.
5. Verify every permission recursively.

---

# Question 9 - Troubleshooting Practical

Without deleting any user or directory, solve the following problems.

1. `developer` cannot execute `deploy.sh`.
2. `tester` cannot read `README.md`.
3. `support` can modify `/backup`.
4. Sticky Bit is missing from `/shared-data`.
5. `resetpass` no longer has SUID permission.

Correct every issue using appropriate commands and verify the result.

---

# Question 10 - Final Integrated Practical

Without creating any new users, complete the following tasks.

1. Ensure all three users can log in.
2. `developer` should successfully execute `deploy.sh`.
3. `tester` should not be able to delete files created by `developer` inside `/shared-data`.
4. Verify that `resetpass` has SUID enabled.
5. Verify that `sharedscript` has SGID enabled.
6. Display the complete permission tree of `/project`.
7. Display user details and password ageing information for all three users.

---


