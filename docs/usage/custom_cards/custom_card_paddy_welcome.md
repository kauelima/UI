---
title: Welcome Custom-card
hide:
  - toc
---
<!-- markdownlint-disable MD046 -->

# Custom-card "Welcome"

This is a `custom-card` to show a welcome message to the user. It comes in four different versions:

- welcome message (*custom_card_paddy_welcome*)
- welcome message + weather-forecast (*custom_card_paddy_welcome_with_weather*)
- welcome message + news (*custom_card_paddy_welcome_with_news*) [based on home-feed-card]

The card shows a time based message (good morning/afternoon/evening/hello) to the logged-in user with username. The second part of the card varies depending on what version you choose. *Note, that you have different requirements depending on the version!*

## Credits

Author: Paddy0174 - 2021
Version: 1.0.0

## Changelog

<details>
<summary>1.0.0</summary>
Initial release
</details>

## Requirements

This card needs the following to function correctly:
<table>
<tr>
<th>Component / card</th>
<th>required</th>
<th>Note</th>
</tr>
<tr>
<td><a href="https://github.com/thomasloven/lovelace-card-mod">lovelace-card-mod</a></td>
<td>yes</td>
<td></td>
</tr>
<tr>
<td><a href="https://www.home-assistant.io/integrations/#search/weather">a weather integration/provider</a></td>
<td>only for <i>welcome-card-with-weather</i></td>
<td>See setup instructions for a HA weather integration</td>
</tr>
<tr>
<td><a href="https://github.com/gadgetchnnel/lovelace-home-feed-card">home-feed-card</a></td>
<td>only for <i>welcome-card-with-news</i></td>
<td></td>
</tr>
</table>

## Usage

```yaml
- type: custom:button-card
  template: custom_card_paddy_welcome
  variables:
    ulm_custom_card_paddy_welcome_time: sensor.time

- type: custom:button-card
  template: custom_card_paddy_welcome_with_weather
  variables:
    ulm_custom_card_paddy_welcome_time: sensor.time
    ulm_custom_card_paddy_welcome_weather_provider: weather.accu_weather

- type: custom:button-card
  template: custom_card_paddy_welcome_with_news
  variables:
    ulm_custom_card_paddy_welcome_time: sensor.time
    ulm_custom_card_paddy_welcome_news_entities:
      - entity: sensor.waste_collection_paper
        content_template: "<b>{{display_name}}</b><br>{{state}}"
      - entity: sensor.waste_collection_waste
        content_template: "<b>{{display_name}}</b><br>{{state}}"
```

## Variables

<table>
<tr>
<th>Card type</th>
<th>Variable</th>
<th>Example</th>
<th>Required</th>
<th>Explanation</th>
</tr>
<tr>
<td>all</td>
<td>ulm_custom_card_paddy_welcome_time</td>
<td>sensor.time</td>
<td>yes</td>
<td>This is your Home Assistant sensor.time</td>
</tr>
<tr>
<td>_welcome_with_weather</td>
<td>ulm_custom_card_paddy_welcome_weather_provider</td>
<td>weather.accu_weather</td>
<td>yes</td>
<td>This is your weather provider</td>
</tr>
<tr>
<td>_welcome_with_news</td>
<td>ulm_custom_card_paddy_welcome_news_entities</td>
<td>entity: sensor.waste_collection_paper<br>content_template: "&lt;b&gt;{{display_name}}&lt;/b&gt;&lt;br&gt;{{state}}"</td>
<td>yes</td>
<td>A <strong>list</strong> of your entities to show in the news feed, see the note underneath.</td>
</tr>
</table>

> #### Note _welcome_with_news

> This card is based on [home-feed-card](https://github.com/gadgetchnnel/lovelace-home-feed-card) and needs a little different configuration in the variables.
> To allow almost all entites from HomeAssistant to show in the news, you're able to set all options that `home-feed-card` provides. This makes it necessary, that you set these entities in a list. Let me show you an example:
>
> ```yaml
> # normally you do this
> variables:
>   ulm_variable: your_variable_entity
>
> # here you need to do a list
> variables:
>   ulm_variable:
>     - your_variable_entity_1
>     - your_variable_entity_2
>
> # to use an option from home-feed-card
> variables:
>   ulm_variable:
>     - entity: your_variable_entity_1
>       content_template: "<b>{{ display_name }}</b><br>{{ state }}"
>     - your_variable_entity_2
> ```
>
> You can find all available options and their description [here](https://github.com/gadgetchnnel/lovelace-home-feed-card#entity-object).
>
> I recommend to use a `content_template` to format the output for every news item, eg. like this:
>
> ```yaml
> content_template: "<b>{{ display_name }}</b><br />{{ state }}"
> ```

## Example

```yaml
- type: custom:button-card
  template: custom_card_paddy_welcome_with_news
  variables:
    ulm_custom_card_paddy_welcome_news_entities:
      - entity: input_datetime.alarm_clock
        content_template: "<b>{{display_name}}</b><br />{{state}}"
      - entity: sensor.waste_collection_bio
        content_template: "<b>{{display_name}}</b><br />{{state}}"
      - entity: sensor.waste_collection_paper
        content_template: "<b>{{display_name}}</b><br />{{state}}"
      - entity: sensor.waste_collection_plastic
        content_template: "<b>{{display_name}}</b><br />{{state}}"
```

## Template code

```yaml
custom_card_paddy_welcome:
  template:
    - ulm_custom_card_paddy_welcome_language_variables
  show_icon: false
  show_name: false
  show_label: false
  styles:
    grid:
      - grid-template-areas: '"item1"'
      - grid-template-columns: 1fr
      - grid-template-rows: min-content
      - row-gap: 12px
    card:
      - border-radius: var(--border-radius)
      - box-shadow: var(--box-shadow)
      - padding: 12px
  custom_fields:
    item1:
      card:
        type: markdown
        content: >
          [[[
            let time = variables.ulm_custom_card_paddy_welcome_time;
            let welcome = '';

            if (time > '18:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_evening;
            } else if (time > '12:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_afternoon;
            } else if (time > '05:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_morning;
            } else {
              welcome = variables.ulm_custom_card_paddy_welcome_hello;
            }

            return welcome + ',<br>' + user.name + '!';
          ]]]
        card_mod:
        style: |
          ha-card {
            border-radius: 14px;
            box-shadow: none;
            font-size: 30px;
            text-align: left;
            cursor: default;
          }

custom_card_paddy_welcome_with_weather:
  template:
    - ulm_custom_card_paddy_welcome_language_variables
  show_icon: false
  show_name: false
  show_label: false
  styles:
    grid:
      - grid-template-areas: '"item1" "item2"'
      - grid-template-columns: 1fr
      - grid-template-rows: min-content min-content
      - row-gap: 12px
    card:
      - border-radius: var(--border-radius)
      - box-shadow: var(--box-shadow)
      - padding: 12px
  custom_fields:
    item1:
      card:
        type: markdown
        content: >
          [[[
            let time = variables.ulm_custom_card_paddy_welcome_time;
            let welcome = '';

            if (time > '18:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_evening;
            } else if (time > '12:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_afternoon;
            } else if (time > '05:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_morning;
            } else {
              welcome = variables.ulm_custom_card_paddy_welcome_hello;
            }

            return welcome + ',<br>' + user.name + '!';
          ]]]
        card_mod:
        style: |
          ha-card {
            border-radius: 14px;
            box-shadow: none;
            font-size: 30px;
            text-align: left;
            cursor: default;
          }
    item2:
      card:
        type: weather-forecast
        entity: "[[[ return variables.ulm_custom_card_paddy_welcome_weather_provider; ]]]"
        show_forecast: false
        card_mod:
        style: |
          ha-card.type-weather-forecast {
            border-radius: 14px;
            box-shadow: none;
          }
          ha-card.type-weather-forecast .state {
            text-align: left;
            font-size: 14px;
            font-weight: bolder;
          }
          ha-card.type-weather-forecast .name {
            text-align: left;
            font-size: 14px;
          }
          ha-card.type-weather-forecast .temp-attribute {
            text-align: right;
          }
          ha-card.type-weather-forecast .temp {
            text-align: right;
            font-size: medium;
            font-weight: bolder;
            margin-right: 16px;
          }
          ha-card.type-weather-forecast .temp span {
            text-align: right;
            font-size: medium;
            font-weight: bolder;
          }
          ha-card.type-weather-forecast .attribute {
            text-align: right;
            font-size: smaller;
          }

custom_card_paddy_welcome_with_news:
  template:
    - ulm_custom_card_paddy_welcome_language_variables
  show_icon: false
  show_name: false
  show_label: false
  styles:
    grid:
      - grid-template-areas: '"item1" "item2"'
      - grid-template-columns: 1fr
      - grid-template-rows: min-content min-content
      - row-gap: 12px
    card:
      - border-radius: var(--border-radius)
      - box-shadow: var(--box-shadow)
      - padding: 12px
  custom_fields:
    item1:
      card:
        type: markdown
        content: >
          [[[
            let time = variables.ulm_custom_card_paddy_welcome_time;
            let welcome = '';

            if (time > '18:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_evening;
            } else if (time > '12:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_afternoon;
            } else if (time > '05:00'){
              welcome = variables.ulm_custom_card_paddy_welcome_morning;
            } else {
              welcome = variables.ulm_custom_card_paddy_welcome_hello;
            }

            return welcome + ',<br>' + user.name + '!';
          ]]]
        card_mod:
        style: |
          ha-card {
            border-radius: 14px;
            box-shadow: none;
            font-size: 30px;
            text-align: left;
            cursor: default;
          }
    item2:
      card:
        type: 'custom:home-feed-card'
        card_id: main_feed
        show_empty: false
        more_info_on_tap: true
        state_color: false
        compact_mode: true
        max_item_count: 3
        show_icons: true
        entities: >
          [[[
            return variables.ulm_custom_card_paddy_welcome_news_entities;
          ]]]
        card_mod:
        style: |
          ha-card {
            border-radius: 14px;
            box-shadow: none;
            font-size: 14px;
            text-align: left;
          }
```

## Custom configuration Example

Here is one example, on how you can extend this (actually every) card. In the end we have the welcome card extended by four buttons to have something like a header with a navigation.

```yaml
- type: horizontal-stack
    cards:
      - type: custom:button-card
        template: edge
      - type: custom:button-card
        template: custom_card_paddy_welcome
        # here we override the styles to extend the welcome card with a second item
        styles:
          grid:
            - grid-template-areas: '"item1" "item2"'
            - grid-template-rows: min-content min-content
            - row-gap: 12px
        custom_fields:
        # here we set up the second item and its contents
          item2:
            card:
              type: horizontal-stack
              cards:
                - type: custom:button-card
                  template: edge
                - type: custom:button-card
                  template: vertical_buttons
                  tap_action:
                    action: navigate
                    navigation_path: /ui-lovelace-minimalist/living
                  icon: "mdi:sofa"
                  name: Living
                  label: Livingroom
                - type: custom:button-card
                  template: vertical_buttons
                  tap_action:
                    action: navigate
                    navigation_path: /ui-lovelace-minimalist/working
                  icon: "mdi:factory"
                  name: Working
                  label: Office
                - type: custom:button-card
                  template: vertical_buttons
                  tap_action:
                    action: navigate
                    navigation_path: /ui-lovelace-minimalist/sleeping
                  icon: "mdi:bed-king-outline"
                  name: Sleeping
                  label: Bedroom
                - type: custom:button-card
                  template: vertical_buttons
                  tap_action:
                    action: navigate
                    navigation_path: /ui-lovelace-minimalist/climate
                  icon: "mdi:air-conditioner"
                  name: Air Conditioning
                  label: Livingroom
                - type: custom:button-card
                  template: edge
      - type: custom:button-card
        template: edge
```
