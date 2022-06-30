#### su/sudo issues

When doing `su root` the OS switches to the user but doesn't load the actual context of the user. In order to load libraries and the profile this is the command that must be done:

```bash
su -
su - <username>
```

