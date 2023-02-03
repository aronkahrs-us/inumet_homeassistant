# INUMET Home Assistant

### Steps
1) Paste the _rest.yaml_ in your config folder
2) At the top of your _configuration.yaml_ paste ```rest: !include rest.yaml```
3) Add the Template Weather Provider, in your _configuration.yaml_ paste 
```
weather:
  - platform: template
    name: "INUMET"
    unique_id: "weather_inumet"
    condition_template: >
          {% set estado = state_attr('sensor.inumet_estado', 'iconoTiempoPresente') %}
          {% if estado == 4 or estado == 13 or estado == 24%}
          cloudy
          {% elif estado == 12 or estado == 19 %}
          windy
          {% elif estado == 9 or estado == 2 or estado == 21 or estado == 22 %}
          partlycloudy
          {% elif estado == 1 %}
          sunny
          {% elif estado == 20 %}
          clear-night
          {% elif estado == 10 %}
          lightning
          {% elif estado == 11 %}
          lightning-rainy
          {% elif estado == 23 or estado == 3 or estado == 5 or estado == 6 or estado == 7 %}
          rainy
          {% elif estado == 16 or estado == 14 or estado == 15 or estado == 8 %}
          fog
          {% elif estado == 17 %}
          snowy
          {% elif estado == 18 %}
          exceptional
          {% endif %}
    temperature_template: "{{ state_attr('sensor.inumet_estado', 'temperatura') | float }}"
    temperature_unit: "°C"
    humidity_template: "{{ state_attr('sensor.inumet_estado', 'humedad') | float }}"
    pressure_template: "{{ state_attr('sensor.inumet_estado', 'presion') | float }}"
    wind_speed_template: "{{ state_attr('sensor.inumet_estado', 'intViento') | float }}"
    wind_speed_unit: "km/h"
    visibility_template: "{{ state_attr('sensor.inumet_estado', 'visibilidad') | float }}"
    visibility_unit: "km"
    wind_bearing_template: "{{ state_attr('sensor.inumet_estado', 'dirViento') | float }}"
    forecast_template: >
                  [
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_0d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 0 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_0d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_0d', 'tempMin') }},
                    },
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_1d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 1 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_1d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_1d', 'tempMin') }},
                    },
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_2d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 2 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_2d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_2d', 'tempMin') }},
                    },
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_3d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 3 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_3d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_3d', 'tempMin') }},
                    },
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_4d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 4 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_4d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_4d', 'tempMin') }},
                    },
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_5d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 5 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_5d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_5d', 'tempMin') }},
                    },
                    {
                      "condition": "{{ states('sensor.inumet_pronostico_6d')|string  }}",
                      "datetime": "{{ (now() + timedelta( days = 6 )).strftime("%Y-%m-%dT00:00:00Z") }}",
                      "temperature": {{ state_attr('sensor.inumet_pronostico_6d', 'tempMax') }},
                      "templow": {{ state_attr('sensor.inumet_pronostico_6d', 'tempMin') }},
                    }
                  ]
```
### Config
As is it's configured for Montevideo, but you can modify it.
You have to change a few things:

For **_Inumet-Alertas_** you can change the City/State for which you want to activate the sensor:
Change ```{% if zona['id'] == 'MONTEVIDEO' %}``` for ```{% if zona['id'] == '<CITY/STATE>' %}```

The CITY/STATE is basically the name of the state in capital letters(it can be done for cities but it's not implemented)
___
For **_Inumet-Estado_** you can change the station that you want the data from:
Change ```json_attributes_path: "$['estaciones'][?(@.estacion=='Prado')]"``` for ```json_attributes_path: "$['estaciones'][?(@.estacion=='<STATION>')]"```

You can get all the variables for this by looking <https://www.inumet.gub.uy/reportes/estadoActual/estadoActualV2.mch>
___
For **_Inumet-Pronostico Nd_** you can change the zone that you want the forecast from:
Change ```{% set pronostico = value_json['items'] | selectattr('zonaCorta', "equalto", "M")|selectattr('diaMasN', "equalto", 0)|first %}``` for ```{% set pronostico = value_json['items'] | selectattr('zonaCorta', "equalto", "<ZONE>")|selectattr('diaMasN', "equalto", 0)|first %}```

You can get all the variables for this by looking <https://www.inumet.gub.uy/reportes/pronosticos/pronosticoV4.json>
