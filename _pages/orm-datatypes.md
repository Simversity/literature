---
layout: page
title: "Datatypes"
category: orm
date: 2014-05-29 21:35:33
order: 2
---

#### DataTypes

All datatypes inherit from simtools.DataType and has following attributes

* default - Default value of the datatype in case the field is absent in the Model.
* nullable {Boolean} - If the datatype can accommodate null values.
* humanize(value) {method} - Transforms the incoming BSON value from DB
* dbfy(value) {method} - Transform the value before sending to DB

Available datatypes in simtools.datatypes

* Unichar - For all text/string purposes
* Regex - Inhertis Unichar but value must match the expression
* ID - Should be a 24 digit string
* Email
* URL
* Boolean
* Integer
* Decimal
* Currency
* Html
* List
* Dict
* Timestamp - Milliseconds since epoch
* Datemonth - Integer ranging from 1 - 12
* Dateday - Integer ranging from 1 - 31
* Timehour - Integer ranging from 0 - 24
* Timeminutes - Integer ranging from 0 - 60