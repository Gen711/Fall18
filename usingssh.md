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



## Navigate to the Jetstream page, and log in

Navigate to https://use.jetstream-cloud.org/

<img src="pics/one.png" class="img-responsive" alt=""> </div>

### Login using your XSEDE credentials

<img src="pics/two.png" class="img-responsive" alt=""> </div>

### Type your stuff in, and select "SIGN IN"

<img src="pics/three.png" class="img-responsive" alt=""> </div>


### To go to settings, click on your user name.

<img src="pics/twelve.png" class="img-responsive" alt="">

## Click "Show More"

<img src="pics/thirteen.png" class="img-responsive" alt="">

## Click "plus sign", enter details
Name the key "jetkey", and paste in key using `command-v` (I sneakily copied the key, above, using the `pbcopy` command). then click confirm.

<img src="pics/fourteen.png" class="img-responsive" alt="">


###If you sit at the same computer each time, you *should* not need to make a new key again.
