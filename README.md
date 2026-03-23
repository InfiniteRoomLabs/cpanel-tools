# cpanel-tools

A collection of command-line tools for managing WHM and cPanel servers. These tools provide easy server management, account querying, and server verification capabilities.

## Features

- Server management (add, remove, edit)
- Account querying and filtering
- Server verification and health checks
- Domain lookup and server matching
- Secure configuration storage

## TODO

- [ ] Add more scripts for different tasks/WHM API endpoints
- [ ] Move common functions to a shared library (API calls, config file parsing, color output, etc.)
- [ ] Add more tests
- [ ] Add more documentation
- [ ] Add more error handling
- [ ] Add more logging
- [ ] Add more configuration options
- [ ] Add more output formats

## Dependencies

- bash
- curl
- jq
- nc (netcat)
- column
- dig (for DNS lookups)
- xclip or pbcopy (optional, for clipboard support)

## Installation

1. Install the dependencies
2. Clone this repository
3. Add the directory the scripts are in to your PATH
4. Create the config directory:

```bash
mkdir -p ~/.config/cpanel-tools
chmod 700 ~/.config/cpanel-tools
```

## Configuration

Create a server configuration file at `~/.config/cpanel-tools/servers` with the following format:

```plaintext
servername:ip_address:type:api_token
```

Example:

```plaintext
myserver:192.168.1.100:whm:your_api_token_here
cpanel1:10.0.0.50:cpanel:another_api_token
```

The configuration file permissions will be automatically set to 600 (user read/write only).

### Fields

- `servername`: Unique identifier for the server
- `ip_address`: Server's IP address
- `type`: Either 'whm' or 'cpanel'
- `api_token`: WHM/cPanel API token

## Available Commands

### server-add

Add a new server to the configuration.

```bash
server-add servername ip_address type token
```

### server-remove

Remove a server from the configuration.

```bash
server-remove servername
```

### server-edit

Edit server details. Can be used in two ways:

1. Interactive editor mode (opens $EDITOR):

```bash
server-edit
```

2. Command-line mode:

```bash
server-edit servername --ip new_ip --type new_type --token new_token
```

### server-list

Display all configured servers with their status.

```bash
server-list
```

### server-verify

Verify server configurations and check for:

- File permissions
- Duplicate entries
- Valid IP formats
- Service accessibility

```bash
server-verify
```

### server-lookup

Look up which server hosts a specific domain.

Options:
-c, --clipboard Copy WHM URL to clipboard
-p, --ping Check if WHM port is responding
-a, --all Show all server details even if IP matches
-d, --dns Show DNS records for the domain
-f, --format FORMAT Output format (text|json) [default: text]
--no-cache Bypass the cache and force a fresh lookup
--clear-cache Clear the entire domain cache
--refresh-cache Force refresh of this domain in the cache
--help Show this help message

```bash
server-lookup domain.com [options]
```

The lookup results are cached for 24 hours by default to improve performance.


### server-account-query

Query account information from WHM servers.

Options:
-t, --type TYPE Search type (domain|owner|user|ip|package)
-v, --value VALUE Search value (PCRE regex)
-m, --method METHOD Search method (exact|regex)
-w, --want FIELDS Comma-separated list of fields to display
-f, --format FORMAT Output format (table|csv|json)

```bash
server-account-query -s servername [options]
```

## Security

The tools implement several security measures:

- Config directory permissions: 700 (user only)
- Config file permissions: 600 (user only)
- Token masking in output
- Validation of all inputs
- Secure API communication

## Examples

1. Add a new WHM server:

```bash
server-add myserver 192.168.1.100 whm your_api_token
```

2. Look up a domain's server:

```bash
server-lookup example.com --ping --dns
```

3. Query accounts on a server:

```bash
server-account-query -s myserver -t domain -v '\.com$' -w 'domain,user,ip,diskused'
```

## License

This project is licensed under the [MIT License](LICENSE).
