# Environment
- `$PASSWORD_STORE_DIR` should be set to a directory. It should contain folders, each one containing login credentials for a specific service. For example `$PASSWORD_STORE_DIR/google/pass.gpg` would be your google password.

- `$PASSWORD_STORE_DIR/blank.gpg` should be a file encrypted by the same key as all other gpg files in the folder. This file is used to test if the gpg key is unlocked and cached, so it shouldn't contain any sensitive data. You can create it like so: `echo blank | gpg -e -r my-gpg-id > $PASSWORD_STORE_DIR/blank.gpg`

- `$DMENU_PROGRAM` should be set to the name of a program like `dmenu`. This means that the program should take a newline seprated list in stdin, and output one entry, chosen by the user, to stdout.

# Configuration
`pw` attempts to automatically enter the correct credentials based on window class & title. This behaviour is defined in the mapfile (`$PASSWORD_STORE_DIR/.map`). Each line in the mapfile defines one association between class/title and pass entries, as shown below (note that `pw` stops at the first line that matches the class & title, so put more specific regexes at the top of the mapfile)
```
<class> /// <title regex> /// <pass subfolder fragment> /// <entry sequence> # optional comment
```

## Matching Class & Title
Class:
- 'browser' will match if window class is `$BROWSER` or appears in `$BROWSERS`
- 'terminal' will match if window class is `$TERMINAL` or appears in `$TERMINALS`
- 'other' will match anything else

Title Regex: matched against window title

## Credential Entry:
Pass Subfolder Fragment: The beginning of the name of a folder in in `$PASSWORD_STORE_DIR` to take credentials from. If multiple subfolders share the given beginning, user will be prompted to choose one (useful if we have multiple accounts with the same service).

Entry Sequence: a string of characters that tells `pw` how to enter your credentials, read character by character:
- lowercase character -> selects an entry in the subfolder starting with that char (uses user input if there are multiple) & types its contents.
- '.' -> allows the user to choose any entry & types its contents
- '$' -> types contents of entry matching system hostname
- '~' -> types contents of entry matching current user's username
- 'T' -> presses the tab key
- 'E' -> presses the enter key
- ' ' -> presses the space key

## example
Here is an example line I have in my personal mapfile:
```
browser /// LinkedIn Login .* LinkedIn /// linkedin /// uTpE
```
It will match when the current window is a browser whose title matches that regex (ie the current tab is the linkedin login page). Let's suppose that in `$PASSWORD_STORE_DIR` I have 2 subfolders: `linkedin-alice` and `linkedin-bob`, each containing `user.gpg` & `pass.gpg`. Then, when I run `pw` with the browser window open, I will first be prompted to choose between the alice and bob accounts. Let's say I choose `linkedin-alice`. Then, `pw` will type the decrypted contents of `linkedin-alice/user.gpg` (the username) press `Tab`, type the contents of `linkedin-alice/pass.gpg` (password) and finally press `Return`, logging me in.

# Installation

# Usage

# Troubleshooting
