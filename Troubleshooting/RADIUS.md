# RADIUS Weirdness
Noticed when RADIUS is enabled, you must have Local set as First Priority, otherwise there will be issues with communication between the Fabric Manager and nodes. You may have issues signing into CLI with local credentials as well, and will
restricted to only using your RADIUS credentials. You also can't use the GUI of the FM to change settings for nodes when this is the case. If this is the case, you may be able to use the following fix that I was able to use.

## Fix
Login to the node that needs changed using CLI

- Use the command `enable` and then `configure terminal` to be able to run the commands needed
- Change priority authentication using the following command: `aaa authentication login default local radius`

Once you've run that on the node, you should be able to manage the node through the FM again, where you will need to go to authentication and change the priority on the GUI as well so that Local is first, and RADIUS is second, then click
"Apply" to apply the change.
