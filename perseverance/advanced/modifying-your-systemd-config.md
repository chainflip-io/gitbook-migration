---
description: For the veterans among us.
---

# Modifying your systemd config

You may want to modify the default command line arguments supplied with the `systemd` config files. If so, **do not edit** the config located in `/lib/systemd/system/`. This will just be immediately overwritten the second you update. Instead, follow the instructions laid out in this StackOverflow post.&#x20;

{% embed url="https://askubuntu.com/questions/659267/how-do-i-override-or-configure-systemd-services" %}
