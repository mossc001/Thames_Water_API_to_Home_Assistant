# Thames Water API to Home Assistant
## Overview

This guide shows you how to query the Thames Water API and get data into Home Assistant. You will be required to sign up to the Thames Water API and currently applications are auto approved: https://data.thameswater.co.uk/s/login/SelfRegister

## API Access
Once registered and confirmed, log in (https://data.thameswater.co.uk/s/login/) and then use the 'Request Access' button that appears above to gain credentials for this API. Approvals are automatic and your credentials will then be available via the 'My Applications' link at the top of the page.

## Home Assistant Integration
Home Assistant uses RESTful to query the Thames Water API. Use the YAML below in your configuration.yaml but with your own 'client_id' and 'client_secret'; note that I've only selected a singular EDM station and the sensors I want from the JSON output.

```
rest:
  - scan_interval: 300
    resource: https://prod-tw-opendata-app.uk-e1.cloudhub.io/data/STE/v1/DischargeCurrentStatus?col_1=LocationName&operand_1=eq&value_1=Thame
    headers:
      Content-Type: application/json
      client_id: YOUR_OWN_ID
      client_secret: YOUR_OWN_SECRET
    sensor:
      - name: "Thame EDM - Alert Status"
        value_template: '{{ value_json["items"][0].AlertStatus }}'
      - name: "Thame EDM - Most Recent Discharge Start"
        value_template: '{{ value_json["items"][0].MostRecentDischargeAlertStart }}'
      - name: "Thame EDM - Most Recent Discharge Stop"
        value_template: '{{ value_json["items"][0].MostRecentDischargeAlertStop }}'
      - name: "Thame EDM - Alert in Past 48hrs"
        value_template: '{{ value_json["items"][0].AlertPast48Hours }}'
```

## RAW JSON Output For A Singular EDM Station
The below code shows the raw output for the "Thame" EDM Station. You can denote the JSON tags for your own sensors in the YAML.
```
{
  "meta": {
    "publisher": "Thames Water Utilities Limited",
    "licence": "https://data.thameswater.co.uk/s/terms-of-service",
    "documentation": "https://data.thameswater.co.uk",
    "version": "1.0.2",
    "limit": "1000"
  },
  "items": [
    {
      "LocationName": "Thame",
      "PermitNumber": "CTCR.1158",
      "LocationGridRef": "SP71150683",
      "X": 471150,
      "Y": 206830,
      "ReceivingWaterCourse": "Lashlake Stream",
      "AlertStatus": "Discharging",
      "StatusChange": "2023-01-13T18:15:00",
      "AlertPast48Hours": true,
      "MostRecentDischargeAlertStart": "2023-01-13T18:15:00",
      "MostRecentDischargeAlertStop": null
    }
  ]
}
```
