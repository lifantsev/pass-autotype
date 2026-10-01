# Mapfile
`pass-autotype` attempts to automatically enter the correct credentials based on window class & title. This behaviour is defined in the mapfile (`$PASSWORD_STORE_DIR/.map`). Each line in the mapfile defines one association between class/title and pass entries, as shown below (note that `pass-autotype` stops at the first line that matches the class & title, so put more specific regexes at the top of the mapfile)
```
<class> /// <title regex> /// <folder name fragment> /// <entry sequence> # optional comment
```

## Matching Class & Title
`<class>`: One of 'browser', 'terminal' or 'other'. Windows are classified using the environment variables `$BROWSER`, `$TERMINAL`, `$BROWSERS`, and `$TERMINALS`.

`<title regex>`: matched against window title using `awk` regex.

## Credential Entry:
`<folder name fragment>`: The beginning of the name of a subfolder of `$PASSWORD_STORE_DIR`. If multiple subfolders share the given beginning, user will be prompted to choose one.

`<entry sequence>`: a string of characters that tells `pass-autotype` how to enter your credentials, read character by character:
- lowercase character -> selects an entry in the subfolder starting with that char (uses user input if there are multiple matches) & types its contents.
- `.` -> allows the user to choose any entry & types its contents
- `$` -> types contents of entry matching system hostname
- `~` -> types contents of entry matching current user's username
- `T` -> presses the tab key
- `E` -> presses the enter key
- ` ` -> presses the space key

## Examples
Here is an example line I have in my personal mapfile:
```
browser /// LinkedIn Login .* LinkedIn /// linkedin /// uTpE
```
It will match when the current window is a browser with the linkedin login page open. Let's suppose that in `$PASSWORD_STORE_DIR` I have 2 subfolders: `linkedin-alice` and `linkedin-bob`, each containing `user.gpg` & `pass.gpg`. Then, when I run `pass-autotype`, I will first be prompted to choose between the alice and bob accounts. Let's say I choose `linkedin-alice`. Then, `pass-autotype` will type the decrypted contents of `linkedin-alice/user.gpg` (the username), press `Tab`, type the contents of `linkedin-alice/pass.gpg` (password), and finally press `Return`, logging me in.

Or an example with the terminal:
```
terminal /// ssh /// ssh-passwords /// .E
```
Matches when the current window is a terminal, and the currently running command contains ssh. Let's say the `ssh-passwords` subfolder contains multiple entries (maybe passwords for different machines I frequently ssh into). Then when I run `pass-autotype`, I will be prompted to choose one of these passwords, it will be typed and the enter key will be hit, logging me into the remote connection.
