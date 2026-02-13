# Helpful Commands
Just a reference for some helpful commands for certain Gigamon releases.

## Port Filter
These commands help you configure a filter to drop PCAP traffic at certain ports
```
config filter deny portsrc <port> alias <new-filter-alias>
config filter deny portdst <port> alias <new-filter-alias>
```
Example: `config filter deny portdst 514 alias DropDST514`
Afterwards you need to save it to a port
```
config port-filter <port> <filter-alias>
```
Example: `config port-filter 3 DropDST514`

## Save Configuration
When you're done making changes, save it to a config file that will be used during the next boot
```
config save <file-name>.cfg nb
```
Example: `config save CONFIG24JUNE2024.cfg nb`
When you're done making changes, give it a good ole `reboot` for the configurations to take affect.
