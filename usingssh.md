Using SSH to connect to your Jetstream instance
---


### Before you launch an instance, go to your terminal and type.

```
ssh-keygen -t rsa -f $HOME/jetkey
```

## Something will happen, and it will ask you for a password - this can be simple (like "password").
After this is done, some stuff will happen (it will look kinda like a techie picture for you, like a diagonal rabbit head).

```
more $HOME/jetkey.pub | cut -d " " -f1-2 | pbcopy
```

## Navigate to the Jetstream page, to settings, by clicking on your user name.

<img src="pics/twelve.png" class="img-responsive" alt="">

## Click "Show More"

<img src="pics/thirteen.png" class="img-responsive" alt="">

## Click "plus sign", enter details
Name the key "jetkey", and paste in key using `command-v` (I sneakily copied the key, above, using the `pbcopy` command). then click confirm.

<img src="pics/fourteen.png" class="img-responsive" alt="">

## Now, launch an instance, and copy the IP address
Wait until the "Status" says active. Tis could be 2-10 minutes.
This is the number like `123.456.789.012`. This is the address to the computer you are trying to connect to.

<img src="pics/ten.png" class="img-responsive" alt="">


## not back to the terminal app. type

```
ssh -i $HOME/jetkey username@xxx.xxx.xxx.xxx
```

and hit enter

###If you sit at the same computer each time, you *should* not need to make a new key again.
