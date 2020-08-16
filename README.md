# Terminal Dashboard for a SAFE Network Vault

**vaultdash** is a Rust command line program which uses [tui-rs](https://github.com/fdehau/tui-rs) to display a dashboard based in the terminal, gathered from one or more logfiles, updated as each logfile grows. 

**Status:** work in progress (but see also [logtail-dash](https://crates.io/crates/logtail) on `crates.io`, which uses an earlier version of this code.) **logtail** is a general purpose version of **vaultdash** for monitoring ordinary logfiles.

## SAFE Network Vault Dashboard
**vaultdash** aims to provide a terminal based graphical dashboard display based of SAFE Network Vault status and activity for a vault on the local machine. It parses input from one or more vault logfiles to gather live vault metrics which are displayed using terminal graphics.

## TODO
- [x] make skeleton app which can parse command line options and display usage
- [x] implement events
  - [x] keyboard events: q = quit
  - [x] resize terminal window
  - [x] make simultaneous with logfile monitoring
- [x] Implement tabbing for Summary / Detail views
- [x] use tui-rs to 'tail' specified logfiles in separate windows
  - [x] watch one or more logfiles specified on the command line
  - [x] send text for each logfile to its own window
  - [x] make a window that scrolls text
    - [x] when the window is full make it scroll to show the last line
    - [x] limit the number of lines retained by a LogMonitor
    - [x] add CLI param: number of logfile lines retain
  - [x] add CLI usage message
- [x] add ability to populate a monitor by processing the logfile from the start
- [x] add CLI param: --ignore-existing logfile content
- [x] fork to make new repo 'logtail' and publish as a Rust util
- [ ] [Issue #1](https://github.com/theWebalyst/vault-dash/issues/1): Implement popup help on ?, h, H
- [ ] Summary view: all vaults on one page
  - [x] just logfile for each vault (divide vertically)
  - [ ] add a storage summary to the left of each logfile
- [ ] Detail view: tab for each vault
  - [ ] Indicate the current logfile (default to the first)
  - [ ] Create empty bands ready for..
  - [ ] h-band1: Heading of logfile and space for some metrics (e.g. size MB)
  - [ ] h-band2: Storage chart / Data Types chart h-bar
  - [ ] h-band3: Activity over time (full width)
  - [ ] h-band4: Logfile (full width)
- [ ] investigate removing tokio to just use standard runtime (see linemux [issue #15](https://github.com/jmagnuson/linemux/issues/15))
- [ ] add some charts
  - [ ] add parsing of dummy logfile input to LogMonitor
  - [ ] use to generate a dummy test chart
  - [ ] update parser to work on real vault log (keeping test logfile as an option)
  - [ ] mock storage chart: horizontal bar chart (vault storage limit/used)
  - [ ] mock chunk metering: vertical bar chart (total, immutable, sequence etc chunks)
  - [ ] get real data into storage chart (poll disk)
  - [ ] get real data into chunk metering

## Install from crates.io

TODO

## Usage

In the terminal type the command and the paths of one or more vault logfiles you want to monitor. For example:

    vaultdash ~/.safe/vault/local-vault/safe_vault.log

When the dashboard is active, pressing 's' or 'd' switches between summary and detail views.
For more information:

    vaultdash --help

## Build
### Get pre-requisites
1. **Get Rust:** see: https://doc.rust-lang.org/cargo/getting-started/installation.html

2. **Get the SAFE CLI:** either download using an install script or build the SAFE CLI locally. Instructions for both options are [here](https://github.com/maidsafe/safe-api/tree/master/safe-cli#safe-cli).

3. **Get the SAFE Vault:** when you have the SAFE CLI working you can install the vault software with the command ` safe vault install` (details [here](https://github.com/maidsafe/safe-api/tree/master/safe-cli#vault-install)).

You are now ready to get vaultdash and can test it by running a local test network as described next.

### Build vaultdash
```
git clone https://github.com/theWebalyst/vault-dash
cd vault-dash
cargo build
```

### Quick Test
Here's a couple of useful commands to build and run vaultdash using Linux logfiles rather than actual vault files. 

Open two terminals and in one run vaultdash with:
```
RUSTFLAGS="$RUSTFLAGS -A unused" cargo run /var/log/auth.log /var/log/apport.log  
```

In a second terminal you can affect the first logfile by trying and failing to 'su root':
```
su root </dev/null
```

You can use any logfiles for this basic level of testing.

### Vault Test
When there is a live test network you will be able to use vaultdash with that, but pre-beta those test networks are only available intermittently. The following therefore shows how to run a local test network and use vaultdash with this.

1. **Start a local test network:** follow the instructions to [Run a local network](https://github.com/maidsafe/safe-api/tree/master/safe-cli#run-a-local-network), but I suggest using the `-t` option to create an account and authorise the CLI with it altogether. As here:
    ```
    safe vault -t run-baby-fleming
    ```
2. **Run vaultdash:** in a different terminal window (so you can continue to use the safe-cli in the first terminal), start vaultdash with:
    ```
    cd vault-dash
    cargo run ~/.safe/vault/local-vault/safe_vault.log
    ```
3. **Upload files using SAFE CLI:** in the SAFE CLI window you can perform operations on the local test network that will affect the vault and the effects will be shown in vaultdash. For example, to [use the SAFE CLI to upload files](https://github.com/maidsafe/safe-api/tree/master/safe-cli#files):
    ```
    safe files put ./<some-directory>/ --recursive
    ```

If you want to try vaultdash with a live network, check to see if one is running at the SAFE Network community forum: https://safenetforum.org

## LICENSE

Everything is GPL3.0 unless otherwise stated. Any contributions are accepted on the condition they conform to this license.

See also ./LICENSE