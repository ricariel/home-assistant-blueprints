# home-assistant-blueprints

Automation blueprints for [Home Assistant](https://www.home-assistant.io/), mostly
around photovoltaics and battery storage: keeping a battery from feeding more into
the grid than it should, and keeping it from running empty.

The blueprint UI texts are German; this file is English.

## Blueprints

| Blueprint | Purpose | Import |
| --- | --- | --- |
| **Solarflow Hub Controler (MQTT)** | Controls charging and feed-in of a Zendure Solarflow hub through MQTT sensors and number entities. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fcontrol-solarflow.yaml) |
| **Solarflow Hub Controler (Zendure HA)** | Same control logic, driven by the entities of the Zendure Home Assistant integration instead of raw MQTT. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fcontrol-zendure-ha.yaml) |
| **Solarflow Reihe Prozentregler** | For hubs wired in series: caps feed-in at a share of total demand (default 90 %). | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fcontrol-solarflow-chain.yaml) |
| **Hoymiles HMS Controler** | Full feed-in control for a Hoymiles HMS inverter. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fcontrol-hms.yaml) |
| **Hoymiles HMS - Einspeiseschutz** | Reduced variant: only prevents feeding more than a configured limit into the grid. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Flimit-hms.yaml) |
| **Heater Controler** | Turns heating in a room down while a window or door is open. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fcontrol-heater.yaml) |
| **Lichtschalter** | Maps a control entity (for example a Shelly switch) onto lights, with brightness and colour temperature applied on switch-on only. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fcontrol-switch.yaml) |
| **Schimmelwarnung Pro** | Watches Mold Indicator sensors and raises notifications plus a daytime voice announcement above a threshold. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fricariel%2Fhome-assistant-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fricariel%2Fschimmelwarner.yaml) |

Clicking an import badge opens the blueprint import dialog in your own Home
Assistant instance with the URL pre-filled.

## Manual Installation

If the import dialog is not available, copy the YAML file into your Home Assistant
configuration:

```text
config/blueprints/automation/ricariel/<blueprint>.yaml
```

Then reload the blueprints, or restart Home Assistant.

## Solar Control Blueprints

The three Solarflow blueprints and the two Hoymiles ones solve overlapping
problems; pick by what your setup exposes:

| Situation | Blueprint |
| --- | --- |
| Solarflow reachable over MQTT (hub taken off the Zendure cloud) | Solarflow Hub Controler (MQTT) |
| Solarflow integrated through the Zendure integration | Solarflow Hub Controler (Zendure HA) |
| Several Solarflow units wired in series | Solarflow Reihe Prozentregler |
| Hoymiles inverter, full control | Hoymiles HMS Controler |
| Hoymiles inverter, only stop excess feed-in | Hoymiles HMS - Einspeiseschutz |

### Why Cap Feed-In Below Demand

The chain blueprint deliberately covers only part of the household demand — 90 %
by default. The remainder is drawn from the grid, which leaves headroom so the
battery is not discharged completely and can still be recharged from the panels.
Matching demand exactly would empty the battery and leave nothing for the next day.

### Requirements

The Solarflow and Hoymiles blueprints read from **sensor** entities and write to
**number** entities. Both have to exist before the blueprint can be used — the
blueprint controls the hub, it does not integrate it.

Sensors should update frequently; the control loop is only as precise as its
input. Ways to get the data into Home Assistant:

- [OpenDTU](https://github.com/tbnobody/OpenDTU) for Hoymiles inverters over MQTT
- [OpenDTU-OnBattery](https://github.com/helgeerbe/OpenDTU-OnBattery)
- [z-master42/solarflow](https://github.com/z-master42/solarflow) for the Zendure MQTT protocol
- [solarflow-bt-manager](https://github.com/reinhard-brandstaedter/solarflow-bt-manager)
  to take a Solarflow hub off the Zendure cloud and point it at a local broker

Once a hub is disconnected from the cloud it listens for commands under
`iot/<product_id>/<device_id>/`.

## Contributing

This project uses `pre-commit` for style and quality checks and Renovate for
dependency updates.

## License

MIT — see [LICENSE](LICENSE).
