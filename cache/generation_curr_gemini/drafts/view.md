---
title: View (Base UI Object)
sidebar_label: View (Base UI Object)
category: views-components
---

# View (Base UI Object)

`Dali::Ui::View` is the fundamental building block for all user interface elements in dali-ui applications. It provides a common set of properties for layout, appearance, and interaction, serving as the base for more specialized UI components.

## Table of Contents
- [Creating and Managing Views](#creating-and-managing-views)
- [View Properties](#view-properties)
  - [Background and Border](#background-and-border)
  - [Corner Radius](#corner-radius)
- [Handling User Interaction](#handling-user-interaction)
- [Managing Selection State](#managing-selection-state)
- [Animating Views](#animating-views)

## Creating and Managing Views

A `Dali::Ui::View` is created using its `New()` static method. Views can be arranged into a hierarchical structure, where parent views contain child views.
