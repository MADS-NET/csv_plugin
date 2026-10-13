# csv plugin for MADS

This is a Sink plugin for [MADS](https://github.com/MADS-NET/MADS). 

Prints every received message as one CSV line on stdout: message timestamp, timecode, agent ID and topic, followed by the fields selected for that topic through JSON pointers.

*Required MADS version: 2.4.3.*

> This plugin is especially designed to work in concert with the [complot](https://github.com/pbosetti/complot) live plotter, which can read CSV data from stdin and plot it in real time. The `fields` setting allows you to select the fields to be plotted for each topic. For example:
> ```sh
> complot p1 --aes "x=timecode y=value g=field"
> mads sink build/csv.plugin | complot p1 -
> ```


## Supported platforms

Currently, the supported platforms are:

* **Linux** 
* **MacOS**
* **Windows**


## Installation

Linux and MacOS:

```bash
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build -j4
sudo cmake --install build
```

Windows:

```powershell
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release
cmake --install build --config Release
```


## INI settings

The plugin supports the following settings in the INI file:

```ini
[csv]
# topic -> JSON pointer (or array of pointers) selecting the output fields
fields = { "sensor" = ["/pos/x", "/pos/y"], "status" = "/ok" }
separator = ","   # output field separator
decimal = "."     # decimal separator for numbers
tidy = true       # one line per field (see below); false = one line per message
```

All settings are optional; if omitted, the default values are used.

With `tidy = true` (default) each selected field gets its own line:
`timestamp,timecode,agent_id,topic,field,value`, where `field` is the JSON pointer
(keypath). A topic without entries in `fields` prints nothing.

With `tidy = false` each message is one line, with columns
`timestamp,timecode,agent_id,topic,<selected fields...>`. A selected
field that is missing prints `NA`; one that is not a scalar (object, array, null)
prints `NULL`. In wide mode, a topic without an entry in `fields` prints only the first four
columns. Strings containing the separator, quotes or newlines are quoted (RFC 4180).
`separator` and `decimal` must be non-empty and different, otherwise the plugin
reports a critical error at the first message.




## Executable demo

Running the test executable (`./build/csv.plugin`) runs a set of self-checks and prints `All checks passed`, exiting non-zero on failure.

---