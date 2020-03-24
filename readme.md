# COVID-19 CLI Summarizer

This is a little script I wrote for myself to track COVID-19 in a taskbar
widget. There are 2 parts: the `covsum` script and the location handler.

## covsum

```
$ covsum
World: 223,027 active, 14,643 deaths

$ covsum -e
World: 223,027 😷 14,643 💀

$ covsum Italy Germany
Italy: 46,638 active, 5,476 deaths | Germany: 24,513 active, 94 deaths

$ covsum -f confirmed,recovered
World: 336,004 confirmed, 98,334 recovered
```

- Hits an [api](https://github.com/nat236919/Covid2019API)
- Caches it (re-downloads when it's 6 hours old)
- Parses & formats it.

```
$ covsum --help

COVID-19 Summarizer v0.1.0

Usage: covsum [OPTIONS] [COUNTRY] [COUNTRY (...)]

Examples:

    covsum

    covsum -e US world

    covsum "united kingdom" -f active,recovered,deaths,confirmed

    covsum Italy Germany Spain

    covsum -C | fzf

Country names are space separated and case insensitive. Wrap multi-word
names in quotes. For global data either use 'world' or leave blank.

Fields may be any combination of 'confirmed', 'recovered', 'deaths',
or 'active'.

Data is saved in ~/.cache/coronavirus_data.json, and will be updated if
more than 6 hours old. Force an update now with the -U flag.

Options:
    -h, --help
    -e, --emojis
    -f, --fields [Fields]            Comma separated, no spaces
    -U, --update                     Force Download
    -C, --countries

```

## covsum-location-handler

```

$ covsum-location-handler --help
covsum location handler
    -h, --help                       Print Help
    -i, --increment                  Increment Location
    -d, --decrement                  Decrement Location

```

Run with no arguments to see currently selected location.

- Select a location from a hard-coded list
- Increments and decrements that location (call these on click & scroll events).
- Send an update signal to waybar when the location changes.

It's not a general purpose tool, it's a starting point for you to customize.

```

$ covsum-location-handler
World

$ covsum-location-handler -i
US

$ covsum-location-handler -i
Poland

$ covsum-location-handler -i
World

$ covsum-location-handler -i
US

$ covsum-location-handler
US

$ covsum-location-handler -d
World

```

## Dependencies

- Ruby >= 2.5

## Installation

Both scripts are self contained; just download/clone and run. You may have to
`chmod +x`, and you'll almost definitely want to modify the location handler for
your use case.

## Integration

Here's how I use it with waybar:

```json
// ~/.config/waybar/config

{
    "modules-right": ["custom/covsum" (...)],
    (...)

    "custom/covsum": {
      "exec": "~/development/covsum/covsum -f active,recovered,deaths -e $(~/development/covsum/covsum-location-handler)",
      "interval": 600,
      "on-click": "~/development/covsum/covsum-location-handler --increment",
      "on-right-click": "~/development/covsum/covsum-location-handler --decrement",
      "signal": 8,
      "tooltip": false
    }
    (...)
}
```

Others will be similar, I'm sure you can figure it out.
