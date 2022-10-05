---
title: The Analytics pane in Power BI visuals
description: This article describes how to create dynamic reference lines in Power BI visuals.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
featuredvideoid: ''
ms.service: powerbi
ms.topic: how-to
ms.subservice: powerbi-custom-visuals
ms.date: 06/18/2019
---

# The Analytics pane in Power BI visuals

The **Analytics** pane was introduced for [native visuals](../../transform-model/desktop-analytics-pane.md) in November 2018.
This article discusses how Power BI visuals with API v2.5.0 can present and manage their properties in the **Analytics** pane.

![The Analytics pane](media/analytics-pane/visualization-pane-analytics-tab.png)

## Manage the Analytics pane

Managing properties in the [**Analytics** pane](../../transform-model/desktop-analytics-pane.md) is very similar to the managing properties in the [**Format** pane](./custom-visual-develop-tutorial-format-options.md). You define an [object](objects-properties.md) in the visual's [*capabilities.json*](capabilities.md) file.

For the **Analytics** pane, the differences are as follows:

### [API 5.0+](#tab/API-5.0+)
* Under the object's definition, add only the object name, property name and type as explained [here](./format-pane.md).
Example: 

```json
{
  "objects": {
    "YourAnalyticsPropertiesCard": {
      "properties": {
        "show": {
          "type": {
            "bool": true
          }
        },
      ... //any other properties for your Analytics card
      }
    }
  ...
  }
}
```

* In formatting settings card specify that this card belongs to analytics pane by set card analyticsPane variable to true, By default card `analyticsPane` variable is false, See the implementations below:

#### [Using FormattingModel Utils](#tab/API-5.0+/Impl-FormattingModel-Utils)
```typescript
class YourAnalyticsCardSettings extends FormattingSettingsCard {
    show = new formattingSettings.ToggleSwitch({
        name: "show",
        displayName: undefined,
        value: false,
        topLevelToggle: true
    });

    name: string = "YourAnalyticsPropertiesCard";
    displayName: string = "Your analytics properties card's name";
    analyticsPane: boolean = true; // <===  Add and set analyticsPane variable to true 
    slices = [this.show];
}
```

#### [Without FormattingModel Utils](#tab/API-5.0+/Without-FormattingModel-Utils)
```typescript
 const averageLineCard: powerbi.visuals.FormattingCard = {
    displayName: "Your analytics properties card's name",
    uid: "yourAnalyticsCard_uid",
    analyticsPane: true, // <===  Add and set analyticsPane variable to true 
    groups: [{
        displayName: undefined,
        uid: "yourAnalyticsCard_group_uid",
        slices: [this.show],
    }]
};
```

### [Old API's](#tab/Old-API)
* Under the object's definition, add the `displayName` and an `objectCategory` field with a value of `2`.

    > [!NOTE]
    > The optional `objectCategory` field was introduced in API 2.5.0. It defines the aspect of the visual that the object controls (1 = Formatting, 2 = Analytics). `Formatting` is used for such elements as look and feel, colors, axes, and labels. `Analytics` is used for such elements as forecasts, trendlines, reference lines, and shapes.
    >
    > If the value isn't specified, `objectCategory` defaults to "Formatting."

* The object must have the following two properties:
    * `show` of type `bool`, with a default value of `false`.
    * `displayName` of type `text`. The default value that you choose becomes the instance's initial display name.

```json
{
  "objects": {
    "YourAnalyticsPropertiesCard": {
      "displayName": "Your analytics properties card's name",
      "objectCategory": 2,
      "properties": {
        "show": {
          "type": {
            "bool": true
          }
        },
      ... //any other properties for your Analytics card
      }
    }
  ...
  }
}
```

You can define other properties in the same way that you do for **Format** objects. And you can enumerate objects just as you do in the **Format** pane.

## Known limitations and issues of the Analytics pane

* The **Analytics** pane has no multi-instance support yet. Objects can't have a [selector](https://microsoft.github.io/PowerBI-visuals/docs/concepts/objects-and-properties/#selector) other than static (that is, "selector": null), and Power BI visuals can't have user-defined multiple instances of a card.
* Properties of type `integer` aren't displayed correctly. As a workaround, use type `numeric` instead.

> [!NOTE]
> * Use the **Analytics** pane only for objects that add new information or shed new light on the presented information (for example, dynamic reference lines that illustrate important trends).
> * Any options that control the look and feel of the visual (that is, formatting) should be limited to the **Formatting** pane.