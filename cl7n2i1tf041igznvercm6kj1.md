## Beautify your git logs

Hi, there 👋 are you also bored of seeing old and boring ```
git logs
```, in this article we will talk about how you can beautify your ugly git logs to look like this :

![Screenshot 2022-09-04 at 1.27.27 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662278275917/HKE_S_HtH.png align="left")
from this:
![Screenshot 2022-09-04 at 1.18.07 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662277878824/-wYd2ISZh.png align="left")

It's super simple, we first need to make an alias, just copy and paste the below code on your git bash or terminal:

```
git config --global alias.lg "log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all"
```
> You can any choose any other color of your choice for the logs

and now every time you need to see your logs, just type in ```
git lg
``` or if you want to see just the lines that changed then type ```
git lg -p
```.

That is pretty much it, if you've liked this article share it with your friends and colleagues!😀