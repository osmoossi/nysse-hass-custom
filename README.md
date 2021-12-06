# nysse-hass-custom
`nysse-hass-custom` is a Home Assistant custom component for real time public transsport information in Tampere. Data is provided via [Digitransit platform](https://digitransit.fi/en/) APIs.

Note! This probably works with other cities provided by [Waltti](https://waltti.fi/) data as well. Only tested with Nysse though.

## Installation

    1. Using a tool of choice open the directory (folder) for HA configuration (where you find configuration YAML file).
    2. If `custom_components` directory does not exist, create one.
    3. In the `custom_components` directory create a new folder called `nysse`.
    4. Download all the files from the this repository and place the files in this directory.
    5. If the files are placed correctly, it should have the hierarchy as: <HA configuration directory>/custom_components/nysse
    6. Restart Home Assistant
    7. Install integration from UI (Configuration --> Intergations --> + --> Search for "nysse")
    8. Specify stop name (e.g. Kullervonkatu 5) or stop code (e.g. 0587). Optionally the route number (e.g. 8) or the destination can be specified as well.
       1. Stop name is case in-sensitive, but stop code is not. E.g. kullervonkatu and Kullervonkatu are OK.
       2. Route takes precedence over destination, if specified. Both options are case in-sensitive.
       3. In case, route and destination are not needed, leave the default values as "ALL" or "all".
       4. Note that some stops may have similar names/codes and you might need to experiment a bit with search terms to get the right one. The database has other cities (Waltti) as well.

<br/>

## Sensor

Sensor provides real time arrival information of a `route` (bus/tram) if available. If real time info is unavailable, it provides the scheduled arrival time of the `route`. If integration is configured with a `route`, sensor provides arrival times filtered for that `route` only. If the integration is configured without a `route`, it provides arrival times for all `routes` arriving at the given stop, in order of their arrival time. Sensor attributes provide the `Stop Name`, `Stop Code`, `Stop GTFS ID` and a list of upcoming `routes` with their arrival times for the day.

<br/>

## UI Options (Entities Card Configuration)

Create a new card on dashboard with the YAML editor. Replace the sensor name with the generated one (in Settings->Entities)
### Option-1
<br/>
Display a stop with attributes showing route, arrival time and destination

![Option-1](resources/images/ui-option-1.png?raw=true)

<br/>

#### Lovalace UI Entities Card Configuration, for Option-2:
```
type: entities
title: Nysse - Kullervonkatu 5
entities:
  - type: attribute
    icon: 'mdi:city-variant'
    entity: sensor.kullervonkatu_5_0587_all
    attribute: STOP NAME
    name: Stop Name
  - type: attribute
    icon: 'mdi:format-title'
    entity: sensor.kullervonkatu_5_0587_all
    attribute: STOP CODE
    name: Stop Code
  - type: attribute
    icon: 'mdi:bus'
    entity: sensor.kullervonkatu_5_0587_all
    attribute: ROUTE
    name: Next Line
  - type: attribute
    icon: 'mdi:city'
    entity: sensor.kullervonkatu_5_0587_all
    attribute: DESTINATION
    name: Destination
  - type: attribute
    icon: 'mdi:clock'
    entity: sensor.kullervonkatu_5_0587_all
    attribute: ARRIVAL TIME
    name: Arrival Time
```
<br/>

### Option-2
<br/>
With a template sensor, more UI options are possible, such as displaying remaining time until next arrival and next upcoming arrival times:

![Option-2](resources/images/ui-option-2.png?raw=true)

<br/>

Create template sensor by adding the following code snipper to your configuration.yaml

#### Template Sensor for next 4 routes at the stop, for Option-2:
```
sensor:
  - platform: template
    sensors:
      next_route_0587_1:
        friendly_name: Next Route - 1
        value_template: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                           {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 0 %}
                             {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[0]['ROUTE'] }}
                           {% else %}
                             'NA'
                           {% endif %}
                         {% else %}
                           'NA'
                         {% endif %}"
        attribute_templates:
          arrival_time: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                    {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 0 %}
                      {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[0]['ARRIVAL TIME'] }}
                    {% else %}
                      'Unavailable'
                    {% endif %}
                  {% else %}
                    'Unavailable'
                  {% endif %}"
          destination: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                          {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 0 %}
                            {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[0]['DESTINATION'] }}
                          {% else %}
                            'Unavailable'
                          {% endif %}
                        {% else %}
                          'Unavailable'
                        {% endif %}"
      next_route_0587_2:
        friendly_name: Next Route - 2
        value_template: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                           {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 1 %}
                             {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[1]['ROUTE'] }}
                           {% else %}
                             'NA'
                           {% endif %}
                         {% else %}
                           'NA'
                         {% endif %}"
        attribute_templates:
          arrival_time: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                    {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 1 %}
                      {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[1]['ARRIVAL TIME'] }}
                    {% else %}
                      'Unavailable'
                    {% endif %}
                  {% else %}
                    'Unavailable'
                  {% endif %}"
          destination: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                          {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 1 %}
                            {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[1]['DESTINATION'] }}
                          {% else %}
                            'Unavailable'
                          {% endif %}
                        {% else %}
                          'Unavailable'
                        {% endif %}"
      next_route_0587_3:
        friendly_name: Next Route - 3
        value_template: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                           {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 2 %}
                             {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[2]['ROUTE'] }}
                           {% else %}
                             'NA'
                           {% endif %}
                         {% else %}
                           'NA'
                         {% endif %}"
        attribute_templates:
          arrival_time: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                    {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 2 %}
                      {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[2]['ARRIVAL TIME'] }}
                    {% else %}
                      'Unavailable'
                    {% endif %}
                  {% else %}
                    'Unavailable'
                  {% endif %}"
          destination: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                          {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 2 %}
                            {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[2]['DESTINATION'] }}
                          {% else %}
                            'Unavailable'
                          {% endif %}
                        {% else %}
                          'Unavailable'
                        {% endif %}"
      next_route_0587_4:
        friendly_name: Next Route - 4
        value_template: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                           {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 3 %}
                             {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[3]['ROUTE'] }}
                           {% else %}
                             'NA'
                           {% endif %}
                         {% else %}
                           'NA'
                         {% endif %}"
        attribute_templates:
          arrival_time: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                    {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 3 %}
                      {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[3]['ARRIVAL TIME'] }}
                    {% else %}
                      'Unavailable'
                    {% endif %}
                  {% else %}
                    'Unavailable'
                  {% endif %}"
          destination: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                          {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 3 %}
                            {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[3]['DESTINATION'] }}
                          {% else %}
                            'Unavailable'
                          {% endif %}
                        {% else %}
                          'Unavailable'
                        {% endif %}"
      next_route_0587_5:
        friendly_name: Next Route - 5
        value_template: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                           {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 4 %}
                             {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[4]['ROUTE'] }}
                           {% else %}
                             'NA'
                           {% endif %}
                         {% else %}
                           'NA'
                         {% endif %}"
        attribute_templates:
          arrival_time: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                    {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 4 %}
                      {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[4]['ARRIVAL TIME'] }}
                    {% else %}
                      'Unavailable'
                    {% endif %}
                  {% else %}
                    'Unavailable'
                  {% endif %}"
          destination: "{% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') != None %}
                          {% if state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES') | length > 4 %}
                            {{ state_attr('sensor.kullervonkatu_5_0587_all', 'ROUTES')[4]['DESTINATION'] }}
                          {% else %}
                            'Unavailable'
                          {% endif %}
                        {% else %}
                          'Unavailable'
                        {% endif %}"
```
<br/>

#### Lovalace Markdown Table with schedule using Entity Filter Card Configuration, for Option-4:
```
type: entity-filter
entities:
  - sensor.next_route_0587_1
  - sensor.next_route_0587_2
  - sensor.next_route_0587_3
  - sensor.next_route_0587_4
  - sensor.next_route_0587_5  
state_filter:
  - operator: '!='
    value: "'NA'"
card:
  type: markdown
  title: Kullervonkatu Next Lines
  content: >-
    | Arrival Time &nbsp;&nbsp;&nbsp;&nbsp; | Route &nbsp;&nbsp;&nbsp;&nbsp; | Destination |

    | :----------- | :---- | :---------- |

    | {% set n = 0 %} {% if config.entities | length > n %} {{
    state_attr(config.entities[n].entity, 'arrival_time') }} | {{
    states(config.entities[n].entity) }} | {{
    state_attr(config.entities[n].entity, 'destination') }} {% endif %} |

    | {% set n = 1 %} {% if config.entities | length > n %} {{
    state_attr(config.entities[n].entity, 'arrival_time') }} | {{
    states(config.entities[n].entity) }} | {{
    state_attr(config.entities[n].entity, 'destination') }} {% endif %} |

    | {% set n = 2 %} {% if config.entities | length > n %} {{
    state_attr(config.entities[n].entity, 'arrival_time') }} | {{
    states(config.entities[n].entity) }} | {{
    state_attr(config.entities[n].entity, 'destination') }} {% endif %} |

    | {% set n = 3 %} {% if config.entities | length > n %} {{
    state_attr(config.entities[n].entity, 'arrival_time') }} | {{
    states(config.entities[n].entity) }} | {{
    state_attr(config.entities[n].entity, 'destination') }} {% endif %} |

    | {% set n = 4 %} {% if config.entities | length > n %} {{
    state_attr(config.entities[n].entity, 'arrival_time') }} | {{
    states(config.entities[n].entity) }} | {{
    state_attr(config.entities[n].entity, 'destination') }} {% endif %} |

    | {% set n = 5 %} {% if config.entities | length > n %} {{
    state_attr(config.entities[n].entity, 'arrival_time') }} | {{
    states(config.entities[n].entity) }} | {{
    state_attr(config.entities[n].entity, 'destination') }} {% endif %} |

```

<br/>

## Original Author
Original project by 
Anand Radhakrishnan [@anand-p-r](https://github.com/anand-p-r)

Forked for HSL->Nysse conversion.
