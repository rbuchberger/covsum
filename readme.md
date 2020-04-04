# COVID-19 CLI Summarizer

This is a little script I wrote for myself to track COVID-19 in a taskbar
widget. There are 2 parts: a `covsum` script and a location handler.

```bash
$ covsum
  World: 1,095,917 confirmed, 58,787 deaths

$ covsum -e
  World: 1,095,917 📈 58,787 💀

$ covsum Italy Germany
  Italy: 119,827 confirmed, 14,681 deaths | Germany: 91,159 confirmed, 1,275 deaths

$ covsum -f confirmed,recovered
  World: 1,095,917 confirmed, 225,796 recovered

$ covsum --help
  COVID-19 Summarizer

  Usage: covsum [OPTIONS] [COUNTRY] [COUNTRY (...)]

  Examples:

      covsum

      covsum -e US world

      covsum "united kingdom" -f active,recovered,deaths,confirmed

      covsum Italy Germany Spain

      covsum -C | fzf | xargs covsum

  Country names are space separated and case insensitive. Wrap multi-word
  names in quotes. For global data either use 'world' or leave blank.

  Fields may be any combination of 'confirmed', 'recovered', 'deaths',
  or 'active'. Note that  the API only returns confirmed, recovered, and
  deaths; 'active' is calculated by subtracting 'recovered' and 'deaths'
  from the confirmed count.

  Data is saved in ~/.cache/coronavirus_data.json, and will be updated if
  more than 6 hours old. Force an update now with the -U flag.

  Options:
      -h, --help
      -e, --emojis
      -f, --fields [Fields]            Comma separated, no spaces
      -U, --update                     Force Download
      -C, --countries

```

It uses this [api](https://github.com/nat236919/Covid2019API), and caches data
for 6 hours.

## covsum-location-handler

This isn't a general purpose tool, it's a starting point for you to adapt.
Its purpose is to cycle through a list of interesting locations, which is
useful because it lets you do this: `covsum $(covsum-location-handler)`

The increment and decrement operations also send a signal to waybar, which I
have configured to reload the module.

Note that there's no configuration here. To change the list of locations, you
need to edit the script itself.

```bash
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

$ covsum-location-handler --help
covsum location handler
    -h, --help                       Print Help
    -i, --increment                  Increment Location
    -d, --decrement                  Decrement Location

```

- Select a location from a hard-coded list. Run with no arguments to see
  currently selected location.
- Increment and decrement that location (call these on click & scroll events).
- Send waybar a signal to update the widget

## Dependencies

- Ruby >= 2.5

## Installation

Both scripts are self contained.

- Download/clone
- You may have to `chmod +x`
- Check that `$ /bin/ruby --version` >= 2.5
- If applicable, modify the location handler for your use case.
- Run

## Integration

Here's my waybar module:

```json
{
  "custom/covsum": {
    "exec": "~/development/covsum/covsum -e $(~/development/covsum/covsum-location-handler)",
    "interval": 600,
    "on-click": "~/development/covsum/covsum-location-handler --increment",
    "on-right-click": "~/development/covsum/covsum-location-handler --decrement",
    "signal": 8,
    "tooltip": false
  }
}
```

Others will be similar, I'm sure you can figure it out.

## License

Do whatever you want.
