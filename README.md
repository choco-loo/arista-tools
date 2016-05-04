# arista-tools

Coming from a Juniper background, with features like configuration archiving, `commit-confirm` and `rollback` - it was alien to use Arista EOS and have no means to undo an incorrect command (short of using out-of-band). So I put together a few tools in the forms of Arista aliases to add the missing functionality.

The idea is to adminster the script using configuration files and loading them, rather than editing directory in the configure terminal.

## Installation

 1. Make the following directories,
     - /mnt/flash/config-archive
     - /mnt/flash/bin
 1. Download and extract the `bin` directory to `/mnt/flash/bin`
 1. Open `bin/defaults` and copy all commands
 1. Paste the commands into the `conf term` to make the aliases available

## Usage

    load [path to config file]

    Eg. load /mnt/flash/new-config

This command will load the new configuration, but not apply it. It can then be compared to the running configuration (for preview of changes), or committed to apply the new configuration.

The load command merely prepares a configuration to apply, but does not apply it until committed.

It can read both a plain text configuration file, and a gzip compressed configuration file.

    commit

This is used following the `load` command, to apply the new configuration without confirmation/rollback.

It will create a configuration archive of the current running configuration, so that it can be rolled back if necessary.

    commit-confirm [minutes]

    Eg. commit-confirm 1

This is used following the `load` command, to apply the new configuration with confirmation/rollback. If the `confirm` command isn't issued within the timeout, then the configuration will automatically be rolled back.

In the event of misconfiguration that leads to a loss of access, this will restore the previous configuration, allowing you to make changes in production and have them rollback if you don't confirm it.

    confirm

This command is used following `commit-confirm` to cancel the rollback and commit the new configuration.

    compare

This is used following the `load` command, to compare the current running configuration to the new configuration that has been loaded. It shows a `diff` style output to display the changes that will be applied upon commit.

    rollback [revision]

    Eg. rollback 0

This command will load a previous revision of the configuration archive (found in `/mnt/flash/config-archive`), which are numbered sequentially from 0 (latest) to 30 (oldest).

This command will both load the configuration, and immediately commit it. So if you need to test the difference first, use `rollback-confirm`

    rollback-confirm [revision] [minutes]

    Eg. rollback-confirm 0 1

This will load the specified revision and `commit-confirm` with the defined timeout. This allows restoration of a previous configuration whilst giving the safety of a rollback if not confirmed.

    save

This command is similar to the native `write`, but in addition to copying `running-config` to `startup-config`, it also archives the configuration, so that it can be used for rollback purposes.

Its best using the save command at all times, to completely replace the `write` command.
