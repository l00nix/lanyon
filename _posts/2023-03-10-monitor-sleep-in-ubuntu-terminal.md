---
title: Monitor Sleep in Ubuntu Terminal
layout: post
---

The command-line tool called setterm can put the monitor to sleep.

Here's how to use setterm to put the monitor to sleep after 5 minutes:

1. Open a terminal window.

2. Type the following command to set the monitor to sleep after 5 minutes:

    ```
    setterm -blank 5
    ```

    This sets the monitor to blank after 5 minutes of inactivity.

3. Press Enter to run the command.

After 5 minutes of inactivity, the monitor will go blank. To wake it up, you can move the mouse or press any key on the keyboard.

To automate putting the monitor to sleep using setterm, you can follow the same steps as described in my previous answer, but replace the xset command with setterm -blank 5 in the script and cron entry.

Use **sudo crontab -e command** to open your cron file and add a new entry to run this script every 5 minutes.

```
*/5 * * * * /usr/bin/setterm -blank 5
```
