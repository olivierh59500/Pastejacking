# Pastejacking

Browsers [now allow](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand) developers to automatically add content to a user's clipboard, following certain conditions. Namely, this can only be triggered on browser events. This post details how you can exploit this to trick a user into running commands they didn't want to get ran, and gain code excicution.

It should also be noted, for some time similar attacks have been possible via [html/css](https://thejh.net/misc/website-terminal-copy-paste).

## Demo

Here is a demo of a website that entices a user to copy an innocent looking command https://security.love/Pastejacking

If a user attempts to copy the text with keyboard shortcuts, ei ctrl+c or command+c, an 800ms timer gets set that will override the user's clipboard with malicious code. 

```bash
echo "not evil"
```

Will be replaced with

```bash
echo "evil"\n
```

Note the newline character gets appended to the end of the line. When a user goes to paste the echo command into their terminal, "evil" will automatically get echoed to the screen without giving the user a chance to review the command before it excecutes. More sophisticated payloads that hide themselves can also be used, such as something [demoed here](https://security.love/Pastejacking/index3.html) and seen below

```bash
touch ~/.evil
clear
echo "not evil"
```

This command will create an evil file in your home directory and clear the terminal out. The victim appears to have the command they intended to copy, nicely pasted into the terminal.


## Impact
This method can be combined with a phishing attack to entice users into running seemingly innocent commands. The malicious code will override the innocent code, and the attacker can gain remote code excicution on the user's host if the user pastes the contents into the terminal.

## How do you protect yourself?
This is not so straight forward. One solution may be to verify the contents of your clipboard before pasting into a terminal, but be careful where you verify these commands. For example if you paste into vim, vim macros may be used to exploit you. An example of this can be seen [in this demo](https://security.love/Pastejacking/index2.html) and below

```javascript
copyTextToClipboard('echo "evil"\n \x1b:!cat /etc/passwd\n');
```

This demo echo evil when pasted in terminal, and it will cat the user's /etc/passwd file when pasted into vim.

One solution around this can be seen below

```bash
"+p       -- within vim to paste clipboard without interpreting as vim command
```

Of course it goes without saying, take note of the source you're pasting from, and exercise additional caution if pasting from questionable sources.
