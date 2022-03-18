# fuck-dpi
tools to help you use applications when you have dual monitors with different DPI... Because it's a pain in the ass to use xrandr and deal with scaling. My eyes hurt, and I'm sure that yours too... So, this is a helper to... run applications with different scalings on the fly in linux <3

## Infer-open-scaling
it uses your mouse position to infer which screen you are on and adjust the DPI of the application to match. Remember to `chmod +x`

```bash
cp infer-open-scaling ~/.local/bin/infer-open-scaling
chmod +x ~/.local/bin/infer-open-scaling
```

Then edit line 2 to add apps that you recognize as electron ones (in order to use `--force-device-scale-factor=1`), and line 7 to setup your boundaries. For reference, I have a 4k and a 2k monitor, in that order from left to right, as such, my boundaries are [0,3840]px for left monitor, everything else is for right monitor.

But you can use whatever thing you want, this is just an example script.

## Scaling modes
By default, if you're on your hi-dpi monitor, then infer-open-scaling will not do anything at all. But, if you're opening an application from another monitor, then first it checks if it is already present as `<command>-scaled` for custom commands (for example, chrome and vscode which requires extra parameters), then if it is a recognized electron app, and finally, if it is not, then it will try scale it to the right DPI by setting environment vars.

## Some special apps...

Chrome and VSCode are nice examples of apps that require custom runners. For the case of Chrome, apart from the usual `--force-device-scale-factor=1`, you need to add `--user-data-dir=/tmp/chrome2/` to the command, and rsync files into a temporary directory in order to create two different renderers... otherwise You'll be stuck with just only one DPI.

VSCode on the other hand, just requires the `--user-data-dir` option configured to some place of your liking. 

Installation examples:

```bash 
cp google-chrome-scaled ~/.local/bin/google-chrome-scaled
cp code-scaled ~/.local/bin/code-scaled
chmod +x ~/.local/bin/google-chrome-scaled
chmod +x ~/.local/bin/code-scaled
```


## Bash completion and application sources

Add the following to your bash/zsh/whatever profile in order to add completion to your infer-open-scaling:

```bash
ascalerAppAutocomplete="grep -roPh 'Exec=\/?(([^\/\s]+\/)+)?(\K[^(\/\s)]+)((?=(\n|\s|\r)*))' /usr/share/applications/ | uniq | paste -sd ' '"
ascalerOptAutocomplete="find /opt -name '*.desktop' -size -50k -type f 2>/dev/null | grep -v 'Permission denied' | xargs -L 1 cat | grep -oP 'Exec=\/?(([^\/\s]+\/)+)?(\K[^(\/\s)]+)((?=(\n|\s|\r)*))' | sort | uniq | paste -sd ' '"
complete -W "$(eval "$ascalerAppAutocomplete") $(eval "$ascalerOptAutocomplete")" infer-open-scaling
```

This will take every `*.desktop` definition from `/usr/share/applications` and `/opt` and add them their respective commands to the completion list, for some ease of use.

Why? Because there is a high chance that if you have an application entry, it's intended to run into a graphical environment, and thus, you can use `infer-open-scaling` to open it in a different DPI. There is no point on using this on a `ls`, there is?

---

Finally, I wish you luck on your journey for the automatic DPI, and I hope that you find this useful... because we're pretty much fucked up until wayland is fully adopted.